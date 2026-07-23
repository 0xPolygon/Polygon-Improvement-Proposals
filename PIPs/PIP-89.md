| PIP            | Title           | Description                                                                                                                                                        | Author                           | Discussion | Status | Type | Date       |
|----------------|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------|------------|--------|------|------------|
| To be assigned | Zurich Hardfork | Heimdall v0.9.0: the height-gated Zurich consensus changes (deterministic state-sync, VEBLOP and checkpoint/milestone/proposal correctness) plus the non-gated transport, failover, bridge and dependency upgrades shipped alongside | Marcello Ardizzone (@marcello33) | TBD        | Draft  | Core | 2026-06-15 |

## Abstract

This PIP specifies the **Zurich** upgrade for Polygon PoS, delivered in the
Heimdall `v0.9.0` release. It has two parts. **Part A** is the Zurich hardfork
proper: a set of consensus-affecting changes, gated by a single per-network
activation height, that make Heimdall behavior deterministic across nodes that
query or re-execute the chain at different times and that add explicit bounds to
consensus-critical handlers. The headline change is **deterministic state-sync
querying**, which lets any node — including a fresh archive re-executing history
from genesis — derive an identical set of state-sync events for a given block.
**Part B** is the set of **non-gated features** shipped in the same release:
a full Heimdall↔Bor gRPC transport (opt-in), multi-endpoint Bor failover,
EIP-1559 dynamic fees for L1 bridge transactions, bridge self-heal and
indexer-free (pruning-friendly) operation, targeted producer replacement, and
dependency upgrades. Part A is consensus-breaking and requires a coordinated
upgrade at the activation height; Part B is opt-in or behavior-preserving and
takes effect on upgrade regardless of fork height. The upgrade is Heimdall-only:
the Bor execution client is unchanged and the gRPC/REST wire formats consumed by
Bor/Erigon are unchanged (the gRPC transport is a new Heimdall→Bor client path,
not a wire-format change).

## Motivation

Two motivations run through this release.

First, **determinism and bounded work in consensus**. Several Heimdall behaviors
were non-deterministic across nodes that query or re-execute the chain at
different times, or lacked explicit limits that every honest validator could
enforce identically. The most consequential is state-sync querying: the legacy
clerk query answers purely on `record_time` against whatever is committed at
query time, so a node replaying a historical block can derive a different set of
committed state-sync events than the original producer did, yielding a divergent
execution-layer state root. Making this — and producer selection, checkpoint
signature aggregation, milestone selection, and proposal/vote-extension
construction — deterministic and bounded is the purpose of the Zurich hardfork
(Part A). These are consensus changes and therefore require a height gate.

Second, **operational robustness and performance** (Part B). Operators need a
faster and more resilient Heimdall↔Bor link (gRPC transport, multi-endpoint
failover), L1 bridge transactions that price correctly under EIP-1559, bridge
recovery that works on pruned nodes without the CometBFT transaction indexer, and
self-heal paths that reconstruct missing checkpoint/state-sync data from
authoritative sources. These do not change consensus and are not height-gated;
they ship in the same release because they are validated and rolled out together.

## Specification

The upgrade activates as the Heimdall `v0.9.0` binary. Part A is additionally
gated by a per-network Heimdall block height via `IsZurichHardfork(height)`,
which is true at and after the activation height and false before it. Part B is
controlled by node configuration (or is behavior-preserving) and is independent
of the fork height.

Zurich activation heights:

| Network        | Heimdall activation height |
|----------------|----------------------------|
| Mainnet        | `47,880,000`               |
| Amoy           | `37,750,000`               |
| Local / devnet | configured per deployment  |

The upgrade is **Heimdall-only**: the Bor execution client is not modified, and
the gRPC/REST request and response shapes consumed by Bor/Erigon are unchanged.

### Part A — Zurich hardfork (height-gated, consensus-affecting)

#### A.1 Deterministic state-sync querying (`x/clerk`)

Before Zurich, the time-bounded record query (`GetRecordListWithTime`, used by
Bor to assemble `StateReceiver.commitState` transactions at sprint boundaries)
returns event records whose `record_time` precedes the requested cutoff,
evaluated against the set committed at query time. Because that set grows over
time, a node querying for a historical block later than it was produced can
receive a different set than the producer did.

From Zurich:

- Event visibility moves from `record_time` (wall-clock) to block height. When an
  event is committed in block `H`, it becomes visible at `H+1`: its
  `visibility_height` is assigned deterministically in the following block's
  `PreBlocker`, and each committed block's `(time → height)` mapping is recorded
  in a reverse index from the activation height onward.
- The time-bounded query resolves the requested cutoff time to a committed
  Heimdall height via that reverse index (gated so the resolved height cannot
  shift between queries), then returns the records visible at that height
  (`visibility_height ≤ resolved height`) whose `record_time` precedes the
  cutoff. The result is a pure function of chain state at/below the resolved
  height and is identical for all callers regardless of query time.
- Records committed before the activation height carry no `visibility_height`.
  For a cutoff that resolves before the index begins (the pre-fork window), the
  query falls back to the legacy `record_time` filter — the exact behavior that
  produced those blocks — so producers and later replayers agree on pre-fork
  blocks too.
- Out-of-order events around the activation boundary are handled explicitly, and
  any error on the visibility path now aborts block processing rather than being
  logged and skipped.
- New gRPC/REST queries expose event-record lists both by height and by time.

The existing gRPC/REST request and response shapes are unchanged; Bor/Erigon
clients require no modification.

#### A.2 VEBLOP producer selection (`x/bor`)

- A `MsgVoteProducers` from a validator not in the current active set is rejected.
- Producer-set scoring sums voting power over active-set validators only, so the
  scoring numerator and the threshold denominator use the same active-set power
  on both sides; stale power from deactivated validators does not contribute.
- A `MsgSetProducerDowntime` carrying a non-default `target_producer_id` is
  validated against the fork-gated eligibility check in `PrepareProposal` and
  `ProcessProposal` (the field itself is described in B.5).

#### A.3 Checkpoint integrity (`app`, `x/checkpoint`)

- Checkpoint signature aggregation counts only vote entries flagged `Commit`;
  non-commit entries are skipped.
- An ante decorator rejects a `MsgCheckpoint` whose `AccountRootHash` is not
  exactly 32 bytes (`common.HashLength`).

#### A.4 Milestone proposition determinism (`x/milestone`)

When selecting the majority milestone proposition, the majority parent-hash
evaluation binds explicitly to the previous milestone's end-block hash (rather
than iterating candidate parent hashes), and that parent-hash match must carry
majority voting power. This guarantees identical selection across validators.

#### A.5 Bounded proposal and vote-extension construction (`app`)

- **Symmetric side-transaction cap:** at most `50` side-transaction responses per
  block. `PrepareProposal` stops including side-transaction-bearing transactions
  once the cap is reached; `ProcessProposal` deterministically rejects proposals
  that exceed it.
- **Wall-clock budgets:** `PrepareProposal` operates under a `500 ms` budget and
  `ExtendVote` under an `800 ms` budget, returning partial results instead of
  overrunning CometBFT timeouts (milestone proposition is skipped when the budget
  is exhausted).
- **Bor-availability tolerance:** `ProcessProposal` and `VerifyVoteExtension`
  treat a "block not found" response from Bor as transient (same handling as a
  query failure) for non-rp vote extensions on checkpoint transactions, avoiding
  spurious proposal rejection while a local Bor is catching up.

#### A.6 Bank-transfer output cap (`app`, ante; cosmos-sdk fork)

An ante decorator caps the aggregate number of bank-transfer outputs per
transaction at `16` (`MsgMultiSend` counts `len(outputs)`, `MsgSend` counts 1),
keeping per-transaction work proportional under the flat-fee model (via the
cosmos-sdk fork `v0.2.11-polygon`).

#### A.7 Stake update on an exiting validator (`x/stake`)

A `StakeUpdate` event for a validator that has already signaled exit
(`EndEpoch != 0`) keeps its voting power at zero; the event is still consumed
(nonce/sequence advance) so the bridge does not resubmit it.

### Part B — Release features (not height-gated)

#### B.1 Heimdall↔Bor gRPC transport (opt-in)

Every Bor-facing call gains a gRPC path, selectable per node via `bor_grpc_flag`
in `app.toml` (with `bor_grpc_url` and `bor_grpc_token`). A startup hash-parity
check compares both transports, and milestone proposition uses a single
round-trip batch call (≈4.4× faster than the HTTP path on devnet benchmarks).
HTTP remains the default; there is no behavior change unless enabled. When
enabled, the connected Bor node must run **Bor `v2.8.3` or newer** (the full Bor
gRPC server).

#### B.2 Bor endpoint failover

`eth_rpc_url` and `bor_grpc_url` accept comma-separated endpoints with automatic
failover, health probing, and metrics (`bor_healthcheck_*`). The primary endpoint
is authoritative and reclaims after recovery. Failover is refused at startup on
block-producing validators as a safety guard.

#### B.3 EIP-1559 L1 bridge transactions

Bridge transactions to Ethereum L1 use EIP-1559 dynamic fees, configurable via
`main_chain_gas_fee_cap` (default 500 gwei) and `main_chain_gas_tip_cap`
(default 10 gwei), replacing the legacy fixed gas-limit / gas-price settings.

#### B.4 Bridge robustness

- **Self-heal extensions:** new recovery methods reconstruct checkpoint and
  state-sync data from authoritative L1/Bor sources when local records are
  incomplete.
- **Indexer-free / pruning-friendly:** the bridge checkpoint flow no longer
  depends on CometBFT's transaction indexer, so it works on pruned nodes; default
  pruning settings are updated accordingly.

#### B.5 Targeted producer replacement

`MsgSetProducerDowntime` accepts an optional `target_producer_id` to designate a
specific replacement producer during planned downtime (the default remains
round-robin). Use of a non-default target is gated by the Zurich hardfork
(A.2).

#### B.6 Operational

Heimdall logs use millisecond-precision ISO-8601 timestamps matching Bor's
format, for cross-service correlation.

### Dependencies

- `0xPolygon/cometbft` → `v0.3.8-polygon`
- `0xPolygon/cosmos-sdk` → `v0.2.11-polygon`
- `0xPolygon/polyproto` → `v0.0.8`
- Bor dependency rebased onto a go-ethereum `v1.17` base
- `golang.org/x/{crypto,net,sys}` and `quic-go` bumped to clear govulncheck advisories

## Rationale

Determinism across query/re-execution time is the unifying goal of Part A. The
state-sync change binds the answer to a frozen Heimdall height rather than
wall-clock-relative `record_time`, because the only way two nodes can agree on a
historical block's state-sync set is to make the query a function of committed
chain state, not of "what is committed right now." The one-block delay for
`visibility_height` keeps freshly committed events out of the deterministic
answer until their height is fixed, which also keeps results stable across short
consensus interruptions. The legacy fallback for pre-fork cutoffs is deliberate:
pre-fork blocks were produced under the `record_time` filter, so reproducing it
for pre-fork cutoffs is what keeps producers and replayers in agreement; it is
not possible to retroactively assign replay-stable visibility to already-committed
pre-fork events, and no state migration is required. Restricting producer voting
and scoring to the active set, binding milestone selection to the last end-block
hash, counting only commit-flag checkpoint signatures, validating the
account-root-hash length, and bounding side-transaction responses, bank-transfer
outputs, and proposal/vote-extension construction time all follow the same
principle: every honest node should validate the same fields, count the same
votes, and enforce the same limits, deterministically.

Part B is kept out of the height gate because none of it changes consensus: the
gRPC transport, failover, EIP-1559 L1 fees, bridge self-heal, indexer-free
operation, and logging are node-local or bridge-local concerns. Bundling them
into the same release as the hardfork lets operators validate and roll out one
binary. The gRPC transport is opt-in (HTTP default) specifically so the fork can
activate without forcing a transport migration, and failover is refused on block
producers to avoid a producer silently following a non-authoritative endpoint.

Alternative considered: separate proposals/forks per change. They are bundled
because Part A items are all Heimdall-only and height-gated and benefit from a
single coordinated activation, and Part B ships in the same binary; sequencing
them would multiply rollout overhead without benefit.

## Backwards Compatibility

Part A is consensus-breaking and requires a coordinated upgrade: every node must
run `v0.9.0` before the activation height. Behavior before the activation height
is unchanged, a node started from pre-fork state replays the pre-fork window
identically (state-sync queries fall back to legacy behavior for pre-fork
cutoffs), and there is no state migration or stored-schema break that requires a
re-sync. Mixed-version operation across the activation height is not supported —
nodes that have not upgraded will fall out of consensus at the height.

Part B is opt-in or behavior-preserving: the gRPC transport defaults off (HTTP
unchanged), failover is additive (single-endpoint config behaves as before), and
the bridge/pruning/logging changes do not alter consensus. Two operator-facing
configuration changes apply on upgrade regardless of fork height:

- **L1 fee config (required):** in `app.toml`, replace the legacy
  `main_chain_gas_limit` / `main_chain_max_gas_price` with the EIP-1559 fields
  `main_chain_gas_fee_cap` (default `500` gwei) and `main_chain_gas_tip_cap`
  (default `10` gwei) before restarting on `v0.9.0`.
- **gRPC to Bor (optional):** to enable, set `bor_grpc_flag = true` with
  `bor_grpc_url` (and `bor_grpc_token` if authenticated); this requires Bor
  `v2.8.3+` on the connected node.

## Test Cases

Consensus-affecting (Part A) changes are covered by boundary tests at `H-1`, `H`,
and `H+1` around the activation height, in the Heimdall-v2 unit suites and the
end-to-end harness:

- **State-sync determinism:** an identical query `(from_id, to_time)` returns an
  identical record set at production time and on later re-execution; a node at a
  post-fork height answering a pre-fork cutoff returns the producer's set (legacy
  fallback), not an empty set; a fresh archive re-executing from genesis across
  the activation height matches validator state roots with no bad blocks.
- **VEBLOP:** votes from inactive validators rejected; scoring excludes
  deactivated power; non-default downtime target gated in Prepare/Process.
- **Checkpoint / milestone:** non-commit signatures excluded; non-32-byte
  `AccountRootHash` rejected; a proposition failing parent-hash continuity not
  selected.
- **Bounds:** proposals exceeding 50 side-tx responses or 16 bank-transfer
  outputs rejected at `ProcessProposal`; Prepare/ExtendVote return within budget;
  stake-update on an exited validator yields zero voting power.

Part B is covered by integration/e2e and devnet validation: gRPC↔HTTP startup
hash-parity, milestone batch-call equivalence, failover health-probing and
reclaim, EIP-1559 L1 submission, and bridge recovery on pruned nodes.

## Reference Implementation

Implemented in Heimdall-v2 (`github.com/0xPolygon/heimdall-v2`), released on the
`v0.9.0` line. Part A is gated by `helper.IsZurichHardfork`; principal locations:
`helper/config.go` (heights and predicate), `x/clerk/keeper` (deterministic
state-sync), `x/bor/keeper` (VEBLOP), `app/abci.go` and `app/vote_ext_utils.go`
(proposal/vote-extension handling, budgets, signature aggregation), `app/ante.go`
and `x/checkpoint/ante` (bank-transfer and account-root-hash bounds),
`x/milestone/abci` (milestone proposition), and `x/stake/keeper` (stake-update).
Part B spans the Bor client transports (HTTP / gRPC / in-process), the bridge
listener/broadcaster, and `helper` configuration.

## Security Considerations

Part A changes are protocol-determinism and protocol-bound rules; each is
evaluated identically by every honest validator from committed chain state, so
none introduces non-deterministic state writes. The state-sync change removes a
source of execution-layer state-root divergence on historical re-execution, and
the explicit bounds (side-transaction responses, bank-transfer outputs,
proposal/vote-extension construction time) keep consensus-critical handlers
bounded and deterministic. Activation-height correctness is itself
security-relevant: the mainnet and Amoy heights above must be wired identically
in every node, and the changes must activate deterministically in
`PrepareProposal`, `ProcessProposal`, `VerifyVoteExtension`, and `PreBlocker` —
proposer-side filtering alone is insufficient. Replay and rollback from pre-fork
state must be preserved, which is why pre-fork cutoffs retain legacy state-sync
semantics.

For Part B, the gRPC transport defaults off and performs a startup hash-parity
check against HTTP so an enabled node cannot silently diverge on Bor-derived
data; `bor_rpc_timeout` is clamped to fit ABCI budgets; and endpoint failover is
refused on block-producing validators so a producer cannot follow a
non-authoritative endpoint. The EIP-1559 fee caps bound L1 fee exposure.

## Copyright

All copyrights and related rights in this work are waived under
[CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
