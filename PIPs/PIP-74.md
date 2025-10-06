---
PIP: 74
Title: Canonical Inclusion of StateSync Transactions in Block Bodies
Description: Make StateSync executions first-class transactions that impact all canonical block fields
Author: Lucca Martins (@lucca30)
Discussion: https://forum.polygon.technology/t/pip-55-canonical-inclusion-of-state-sync-transactions-in-block-bodies/](https://forum.polygon.technology/t/pip-55-canonical-inclusion-of-state-sync-transactions-in-block-bodies
Status: Draft
Type: Core
Date: 2025-08-19
---

## Abstract

Today, Polygon PoS executes StateSyncs during `CommitStates` at the **Finalize** step of Bor. These executions mutate the state DB (and thus the `stateRoot`) but are **not recorded in the block’s transaction list**, so they **do not affect `transactionsRoot`, `receiptsRoot`, or `logsBloom`**.
This PIP proposes adding a **single, canonical StateSync transaction** appended to each block that had StateSync activity. The transaction **does not execute EVM code**, has **empty data**, **zero gas/fees**, and exists solely to **anchor in the block roots** the membership and outcomes of all StateSync events executed in that block. A **hard fork** is required because this changes post‑fork block hashing and Merkle roots.

This proposal preserves [PIP‑12](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-12.md) eligibility, [PIP‑20](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-20.md) observability, and [PIP‑36](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-36.md) replay semantics. It changes only **representation**: StateSync results become part of the canonical transaction/receipt set, improving root‑based validation, snap‑sync trustlessness, storage/RPC simplicity, and operator visibility.

## Motivation

* **Canonicality & proofs:** Today you cannot prove from block data alone that StateSyncs happened in block *N*. One **synthetic StateSync transaction per block** commits membership and results to `transactionsRoot`, `receiptsRoot`, and `logsBloom`, enabling Merkle proofs without replaying state.
* **Observability:** PIP‑20 success/failure signals become part of a **canonical receipt** and bloom.
* **Consistency checks & snap‑sync:** Inclusion and outcomes are committed by roots, enabling trustless verification in snap‑sync.
* **Simplicity:** Removes side‑channel storage and RPC flags; standardizes on a typed transaction/receipt.

## Specification

### 1) Transaction: **StateSyncTx** (single per block, optional)

Bor **MUST** append **one** StateSyncTx **if** the block executed ≥1 eligible StateSync event (per PIP‑12). If none were executed, no StateSyncTx is present.

**Envelope (consensus‑visible):**

* `type`: `0x7F` (typed, system transaction; not user‑signed)
* `from`: `0x0000000000000000000000000000000000000000`
* `to`:   `0x0000000000000000000000000000000000000000`
* `nonce`: `0`
* `value`: `0`
* `gasLimit`: `0`
* `maxFeePerGas` / `maxPriorityFeePerGas`: `0`
* `data`: `0x` (empty)

**Semantics:**

* **No EVM execution** and **no state change**. The transaction exists to bind a **receipt with logs** that summarize all StateSync events executed in `Finalize` for this block.
* **No gas increase:** The StateSyncTx does **not** increase `gasUsed` for the block. Per‑event execution remains bounded by the client‑enforced `txGasLimit` during `Finalize` as in PIP‑36.

**Placement:** Append **after all user transactions** in the block’s transaction list.

> **Compatibility with PIP‑36:** PIP‑36 specifies an internal per‑event `txGasLimit` “so the receiver doesn’t consume all available gas… (state syncs are batched together in one txn).” This PIP **keeps** the single‑transaction model, formalizes its canonical encoding, and commits it to the roots while keeping block gas unchanged.

### 2) Inner payload (RLP) and transaction hash

The typed transaction’s **inner payload** encodes the executed event metadata, used solely for hashing/transport:

```go
// StateSyncData summarizes one executed event
struct StateSyncData {
  ID:       uint64            // stateId
  Contract: address           // receiver contract on PoS
  Data:     bytes             // payload bytes (wire shape only; not EVM-executed here)
  TxHash:   bytes32           // L1 tx hash that emitted the event
}

// wire shape for RLP
struct encStateSyncData {
  ID       uint64
  Contract address
  Data     bytes
  TxHash   bytes32
}

// StateSyncTx.inner is RLP([]encStateSyncData) in ascending ID, contiguous, no gaps
```

**Hashing (avoid circular dependency):**

* **Before (legacy synthetic hash):** `txHash = keccak256("matic-bor-receipt-" || blockNumber || blockHash)` — circular with `blockHash`.
* **After (this PIP):** use standard typed‑tx hashing:
  `txHash = keccak256( type || rlp(inner) )`
  (i.e., `prefixedRlpHash(tx.Type(), tx.inner)` in geth terms).

### 3) Receipt & logs

The block’s receipt array gains **one** receipt for the StateSyncTx:

* `status = 1` (unless batch anchoring fails due to client error)
* `gasUsed = 0`
* `logs`: one or more entries encoding, per event, at least:

  * `stateId` (ascending, contiguous),
  * `receiver` address,
  * `success/failure` bit,
  * optional payload hash and L1 `TxHash`.

These logs commit PIP‑20 observability to `receiptsRoot` and `logsBloom`.

### 4) Block header & body effects

* **`transactionsRoot`** — recomputed over `[UserTxs || StateSyncTx]`.
* **`receiptsRoot`** — includes the StateSyncTx receipt with per‑event logs.
* **`logsBloom`** — includes bloom of all batch logs.
* **`stateRoot`** — unchanged (state changes still applied in `Finalize`).
* **`gasUsed`** — **unchanged** (StateSyncTx uses zero gas).
* **`baseFeePerGas`** — unaffected.
* **Other header fields** — unchanged.

### 5) Correctness & consensus validity

A post‑fork block is **invalid** if any of the following holds:

1. A StateSyncTx is present but **no** StateSync event was executed in `Finalize`, or eligible events existed but **no** StateSyncTx is appended.
2. The inner list is not **strictly ascending** by `stateId` with **no gaps** from the first executed `stateId`.
3. The StateSyncTx is **not** appended **after** all user transactions.

> If execution of events stops early due to PIP‑36’s per‑event `txGasLimit`, the inner list **must equal the contiguous prefix actually executed** in this block; remaining events are executed (and later committed) in subsequent blocks.


## Backwards Compatibility

* **Consensus‑breaking:** Yes; tx list and roots change post‑fork.
* **Pre‑fork history:** Unchanged (no StateSyncTx).
* **Tooling:** Clients/indexers must recognize `type=0x7F` and parse its receipt/logs; no txpool admission.

## Security Considerations

* **DoS bounds:** Per‑event execution remains bounded by PIP‑36’s `txGasLimit`; the synthetic StateSyncTx itself uses `gas=0`.
* **Determinism:** PIP‑12 windowing plus contiguous ascending `stateId` ordering ensure all nodes produce identical inner payloads.
* **Economic neutrality:** No fees/tips/burn; `gasUsed` and base‑fee trajectory unaffected.

### Implementation Considerations

* **Types:** Introduce `StateSyncTx` implementing the geth `TxData` interface with zero‑valued accessors and RLP encode/decode of `[]encStateSyncData`. Its `sigHash`, `setSignatureValues`, and similar EOA‑only paths MUST be unreachable.
* **Hashing:** Use standard typed‑tx hashing (`prefixedRlpHash(type, inner)`); **do not** derive from block number/hash.
* **RPC:** Expose the synthetic tx in `eth_getBlockBy*`; its receipt via `eth_getTransactionReceipt` (`gasUsed=0`, per‑event logs).
* **Legacy import:** Pre‑fork blocks continue to apply StateSync effects out‑of‑list using Heimdall as the source of truth; any synthetic artifacts for history are **telemetry only**.

## References

* **PIP‑12:** Time‑Based StateSync Confirmation Delay — [https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-12.md](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-12.md)
* **PIP‑20:** State‑Sync Verbosity — [https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-20.md](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-20.md)
* **PIP‑36:** Replay Failed State Syncs — [https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-36.md](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-36.md)

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
