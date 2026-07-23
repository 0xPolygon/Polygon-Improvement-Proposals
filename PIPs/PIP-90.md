| PIP | Title           | Description                                                                                                                                                                                                                  | Author                                                            | Discussion | Status | Type | Date       |
|-----|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|------------|--------|------|------------|
| 90  | Ithaca Hardfork | Heimdall v0.10.0: the height-gated Ithaca consensus change that lets a stalled block producer be rotated out even while a milestone is pending, plus the non-gated mempool, logging and dependency changes shipped alongside | Marcello Ardizzone (@marcello33), Pratik Patil (@pratikspatil024) | TBD        | Draft  | Core | 2026-07-13 |

## Abstract

This PIP specifies the **Ithaca** upgrade for Polygon PoS, delivered in the
Heimdall `v0.10.0` release. It has two parts. **Part A** is the Ithaca hardfork
proper: a consensus-affecting change, gated by a single per-network activation
height, that restores autonomous liveness recovery when a Bor block producer
stalls **while a milestone is pending** (`1/3 ≤ PM < 2/3`). Before Ithaca a
pending milestone unconditionally suppressed span rotation, so a producer that
stalled inside that band wedged the chain with no self-recovery. Ithaca adds a
rule that forces a span rotation from the `>1/3`-agreed actual Bor head when
that head stops advancing for a stall threshold, without reorging the blocks the
producer already made, and hardens the VEBLOP producer-selection path against an
empty candidate set. **Part B** is the set of **non-gated changes** shipped in
the same release: a `CheckTx` per-transaction message cap (mempool DoS guard), a
transaction-fee nil-guard shipped via a Cosmos SDK fork bump, and granular
per-module log-level filtering.
Part A is consensus-breaking and requires a coordinated upgrade at the activation
height; Part B is node-local and takes effect on upgrade regardless of fork
height. The upgrade is **Heimdall-only**: the Bor execution client is unchanged
and the gRPC/REST wire formats consumed by Bor/Erigon are unchanged.

## Motivation

Zurich (PIP-89) made Heimdall's producer-liveness machinery — VEBLOP producer
selection, milestone selection, span rotation on stall — deterministic and
bounded. It left one liveness gap. The pending-milestone band (`1/3 ≤ PM < 2/3`)
is the state where enough validators have voted for a milestone proposition to
register it, but not the `2/3` needed to finalize. In that band, span rotation
was unconditionally suppressed to avoid rotating a producer whose blocks were
about to be finalized. But if the producer **stalls** while a milestone sits in
that band — it stops extending the Bor head and the milestone never reaches
`2/3` — the suppression means no new producer is ever selected. The chain has no
autonomous way out and halts until manual intervention. This is the
~15-minute halt reproduced in the VEBLOP liveness stress bench.

Ithaca closes that gap: a pending milestone no longer grants a stalled producer
indefinite immunity. When the network agrees (`>1/3`) that the actual Bor head
has stopped advancing for the stall threshold, a rotation is forced from that
head onward. This is a consensus change — it alters which producer a span
resolves to at a given height, and it is enforced identically by every validator
in `PreBlocker` — so it must be height-gated.

## Specification

The upgrade activates as the Heimdall `v0.10.0` binary. Part A is additionally
gated by a per-network Heimdall block height via `helper.IsIthaca(height)`, which
is true at and after the activation height and false before it. Part B is
node-local (or behavior-preserving) and is independent of the fork height.

Ithaca activation heights (Heimdall block height):

| Network        | Heimdall activation height | Approx. wall-clock (UTC)      |
|----------------|----------------------------|-------------------------------|
| Mainnet        | `50,185,000`               | 2026-07-29 ~14:00             |
| Amoy           | `40,776,000`               | 2026-07-20 ~14:00             |

### Part A — Ithaca hardfork (height-gated, consensus-affecting)

#### A.1 Span rotation on a stalled pending Bor head (`app`, `x/milestone`)

Under a pending milestone, `PreBlocker` now evaluates whether the Bor head has
stalled and, if so, rotates the producer:

- **Agreed actual head.** Each validator carries its actual latest observed Bor
  head in its milestone vote extension via two new `MilestoneProposition` fields,
  `latest_block_number` (field 5) and `latest_block_hash` (field 6), emitted only
  post-fork. `PreBlocker` tallies a `>1/3`-agreed head via `GetMajorityActualHead`,
  selecting the head that carries the **greatest summed voting power** above the
  threshold. The rotation keys on this agreed actual head — not on the milestone
  proposition tail, which is capped at `MaxMilestonePropositionLength` (10) blocks
  and can lag the real head — so blocks the producer made beyond the proposition
  window are preserved, not reorged.
- **Bound.** The agreed head is bounded by the last span's `EndBlock` (the
  furthest scheduled runway). An honest producer cannot advance beyond the
  scheduled spans, so a reported head past that bound is fabricated and is
  dropped before the tally.
- **Stall clock.** The agreed head and its hash identity are tracked across
  blocks (new milestone store keys, below). The stall clock restarts whenever the
  head or its identity changes, so continued healthy production never ages it. A
  fresh observation is never itself treated as a stall.
- **Rotation.** When the agreed head is unchanged for longer than
  `GetBorStallThreshold` (which reuses the existing change-producer threshold), a
  new VEBLOP span is minted starting at `agreedHead + 1`, ending at the last
  span's `EndBlock` so it fully supersedes the stalled span's runway. The
  replacement producer is drawn from the `2/3`-fed latest-active-producer set
  (mirroring the current-span rotation path), **not** from the pending
  milestone's `1/3` supporters, and the stalled and previously-failed producers
  are excluded. Both rotation clocks are debounced by the span-rotation buffer
  after a rotation so the freshly installed producer is not immediately rotated
  out again.
- **Fail-safe.** When no `>1/3` actual head is agreed (heads not yet converged,
  or pre-fork vote extensions at the activation boundary), rotation is skipped
  without resetting a running clock; the truncated proposition tail is never used.
  Out-of-range, `MaxUint64`, and span-exhaustion boundaries are guarded so the
  path can never overflow or return an error out of `PreBlocker`.

New consensus-tracked state (milestone module, written only post-fork):
`PendingBorBlockPrefixKey` (`0x85`), `PendingBorBlockIdPrefixKey` (`0x86`),
`PendingBorBlockHeightPrefixKey` (`0x87`).

#### A.2 VEBLOP empty-candidate fallback (`x/bor`)

`SelectNextSpanProducer` could hand an empty candidate slice to `SelectProducer`
when the elected producer set collapsed to the current producer and it was then
filtered out of the active set; in the future-span path that error propagated out
of `PreBlocker`. Ithaca adds a deterministic fallback that draws a replacement
from the caller's active/supporting set, then the full validator set — each
sorted and excluding the current and excluded producers — so future-span creation
always has a candidate. The fallback is an explicit opt-in used only by the
future-span path (`checkAndAddFutureSpan`); the non-fatal rotation paths keep
their pre-existing skip-and-retry behavior. The exclusion set is applied before
the empty check and re-applied to the fallback candidates, so an exclusion that
empties a non-empty active set re-triggers the fallback rather than wedging.

### Part B — Release changes (not height-gated)

#### B.1 `CheckTx` per-transaction message cap (`app`)

`CheckTx` caps a single transaction at **16 messages** (`maxMsgsPerTx`). An
oversized multi-message transaction would otherwise trigger the per-message
VEBLOP validation loop and full ante processing on every gossip hop. The
transaction is still decoded once to count its messages; over-cap transactions
are rejected with `ErrTxTooLarge`. Enforced in `CheckTx` only — block validity
and consensus are unaffected.

#### B.2 Transaction fee nil-guard (`x/auth`, cosmos-sdk fork)

The transaction wrapper's fee accessors (`GetGas`, `GetFee`, `FeePayer`,
`FeeGranter`) now return zero / `nil` when a decoded transaction carries no
`AuthInfo.Fee`, instead of dereferencing it. A transaction with no fee set can be
decoded during mempool admission (`CheckTx`) and gossiped between nodes; without
the guard, the first fee-reading ante decorator dereferences the nil fee and
panics on the node processing it. The guard lets a fee-less transaction fail
validation cleanly instead. This is node-local robustness hardening: it does not
change consensus, and it does not change fee handling for well-formed
transactions (which always carry an `AuthInfo.Fee`). It ships via the cosmos-sdk
fork bump `v0.2.11-polygon` → `v0.2.12-polygon`.

#### B.3 Granular per-module logging (`helper`, `cmd`, packaging)

`log_level` accepts a per-module form in addition to a single global level:
either a single level applied to every module (e.g. `"info"`), or a
comma-separated list of `module:level` pairs with an optional `*:level` default
(e.g. `"*:info,mempool:debug"`), scoping `debug` to one subsystem without the
volume of a global debug level. The module key is the `module` field of a log
line (CometBFT: `consensus`, `p2p`, `mempool`, `blocksync`, `statesync`, …;
Heimdall: `server`, `x/bor`, `x/checkpoint`, `x/milestone`, `bridge/processor`,
…). Documented in the packaged mainnet/Amoy `config.toml`.

### Dependencies

- `0xPolygon/cosmos-sdk` fork `v0.2.11-polygon` → `v0.2.12-polygon` (carries the
  fee nil-guard in B.2; no other functional change).

## Rationale

The design keys the stall detection and rotation on the **agreed actual Bor
head** rather than the milestone proposition tail because the proposition is
capped at 10 blocks and can lag the true head; rotating from the tail would
reorg the blocks the producer legitimately made beyond that window. Carrying the
actual head in the milestone vote extension lets every validator tally the same
head deterministically in `PreBlocker`.

`GetMajorityActualHead` selects by **greatest voting power** above `1/3`, not by
highest block number. During a genuine stall honest validators converge on the
real tip, so a `>1/3` byzantine minority reporting a fabricated higher head can
never outvote the converged honest majority — this also removes the
rotating-hash clock-reset vector, since a minority head never wins the tally. The
replacement producer is drawn from the `2/3`-fed active producer set rather than
the pending milestone's `1/3` supporters (which a byzantine slice can steer via
the per-block hash tie-break), and the agreed head is bounded by the scheduled
runway so it cannot point past honest progress. The residual — a byzantine slice
holding strictly more power than any honest agreement steering the head **within**
the runway — is only reachable while honest votes are fragmented, which a real
stall removes by converging them; even then it cannot reorg finalized blocks or
choose the new producer.

The threshold reuse (`GetBorStallThreshold` = the change-producer threshold) and
the shared span-rotation buffer keep the new path consistent with the existing
current-span rotation policy validated in the VEBLOP liveness bench, while
remaining a distinct getter so it can be tuned independently later.

Part B is kept out of the height gate because none of it changes consensus: the
`CheckTx` cap and the fee nil-guard are mempool-local admission-control
robustness, per-module logging is node-local, and none carries a state-shape
change. Bundling them into the same release lets operators validate and roll out
one binary.

Alternative considered: a separate fork per change. Rejected for the same reason
as Zurich — the single consensus change benefits from one coordinated
activation, and the node-local changes ship in the same binary; sequencing them
would multiply rollout overhead without benefit.

## Backwards Compatibility

Part A is consensus-breaking and requires a coordinated upgrade: every node must
run `v0.10.0` before the activation height. Behavior before the activation height
is unchanged — a pending milestone suppresses rotation exactly as it did on
`v0.9.0` — and a node started from pre-fork state replays the pre-fork window
identically. Mixed-version operation across the activation height is not
supported: nodes that have not upgraded will diverge at the height.

There is **no state migration and no module `ConsensusVersion` bump**. The
pending-bor-head tracking keys (`0x85`–`0x87`) are new and are only ever written
at or after the activation height, so there is no existing data to migrate and no
change to how pre-fork blocks are processed. The new `MilestoneProposition`
fields (`latest_block_number`, `latest_block_hash`) are appended proto fields
emitted only post-fork; pre-fork propositions carry them empty. The two fields
are populated together or not at all, and unknown/extra vote-extension fields are
rejected in `VerifyVoteExtension`.

Part B is node-local and behavior-preserving on upgrade: the `CheckTx` cap only
rejects transactions carrying more than 16 messages (well above any legitimate
Heimdall transaction), per-module logging defaults to the same global-level
behavior when a single level is configured, and the dependency bump carries no
consensus or stored-state change. No operator configuration change is required,
though operators may opt into `module:level` log filtering.

## Test Cases

The consensus-affecting (Part A) change is covered by boundary tests at `H-1`,
`H`, and `H+1` around the activation height in the Heimdall-v2 unit suites and by
the VEBLOP liveness bench:

- **Pending-stall rotation:** an unchanged `>1/3`-agreed head past the stall
  threshold rotates the producer from `head+1` with no reorg; a still-advancing
  head resets the clock and does not rotate; no `>1/3` agreement skips rotation
  without resetting the clock; a pre-fork height under a pending milestone
  retains the legacy suppress-and-log behavior.
- **Adversarial head selection:** a `>1/3` byzantine minority reporting a
  fabricated higher head cannot win the tally (greatest-voting-power selection);
  an out-of-range head (beyond the scheduled runway) is dropped before the tally
  even when it holds greater voting power; a same-height head with a mismatched
  hash is rejected in vote-extension validation.
- **Boundaries:** span-exhaustion (`head == lastSpan.EndBlock`), `MaxUint64`
  head, and unbounded pending head are all handled without overflow or an error
  return out of `PreBlocker`; re-rotation under a persistent stall excludes the
  just-installed producer; the debounce prevents an immediate re-rotation.
- **VEBLOP fallback:** an exclusion that empties a non-empty active set
  re-triggers the fallback instead of handing `SelectProducer` an empty slice;
  the concrete selected producer is asserted.

Part B is covered by unit tests (per-tx message-cap rejection, the fee-less
transaction repro in the cosmos-sdk fork, per-module log-level filtering) and
standard CI.

## Reference Implementation

Implemented in Heimdall-v2 (`github.com/0xPolygon/heimdall-v2`), released on the
`v0.10.0` line, tracked under POS-3629. Part A is gated by `helper.IsIthaca`;
principal locations: `helper/config.go` (heights, `IsIthaca`,
`GetBorStallThreshold`), `app/pending_stall.go` (stall detection and rotation),
`x/milestone/abci/abci.go` (`GenMilestoneProposition` actual-head emission,
`GetMajorityActualHead` tally, `validateLatestHead`),
`x/milestone/types/keys.go` (tracking keys),
`proto/heimdallv2/milestone/milestone.proto` (vote-extension fields), and
`x/bor/keeper/veblop.go` / `x/bor/keeper/veblop_producer_fallback.go` (empty-
candidate fallback). Part B: `app/app.go` (`maxMsgsPerTx`), the cosmos-sdk fork
`x/auth/tx/builder.go` fee-accessor nil-guards (`v0.2.12-polygon`, wired via
`go.mod`), and `helper/log.go` / `cmd/heimdalld/cmd/root.go` (per-module
logging).

Comparison for this release:
`https://github.com/0xPolygon/heimdall-v2/compare/v0.9.0...v0.10.0-beta-candidate`.

## Security Considerations

The pending-stall rotation is a `PreBlocker` state change and is evaluated
identically by every honest validator from committed chain state and the tallied
vote extensions; it makes no external RPC call, uses no wall-clock time, and
iterates no unsorted map. The head tally, its voting-power selection, the
runway bound, and every boundary guard are the security surface, because the
alternative to a correct rotation is either a chain halt (the bug this fixes) or
a byzantine-steered rotation. The design confines a `>1/3` byzantine minority to,
at most, steering the agreed head within the already-scheduled runway while
honest votes are fragmented — it can never reorg finalized blocks, point the head
past honest progress, or select the replacement producer (which comes from the
`2/3`-fed active set). Activation-height correctness is itself security-relevant:
the mainnet and Amoy heights above must be wired identically in every node in the
same `helper/config.go` initialization path, and the behavior must activate
deterministically in `PreBlocker` for all validators — proposer-side handling
alone is insufficient. Replay and rollback from pre-fork state are preserved
because the tracking keys and proposition fields are only written/emitted from
the activation height onward, so no pre-fork block is reprocessed differently.

For Part B, the `CheckTx` message cap is admission control only and cannot affect
block validity or consensus; it reduces the per-gossip-hop work an oversized
multi-message transaction can impose. The fee nil-guard closes a nil-pointer
dereference on the fee accessors of a decoded fee-less transaction — reachable
during mempool admission of a gossiped transaction — so that such a transaction
is rejected cleanly rather than panicking the processing node; it is defensive
and changes no fee behavior for well-formed transactions. Per-module logging
carries no consensus implication.

## Copyright

All copyrights and related rights in this work are waived under
[CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
