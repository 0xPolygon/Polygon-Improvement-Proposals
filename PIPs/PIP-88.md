---
PIP: 88
Title: Cold-Storage and Precompile Gas Repricing
Author: Krishang Shah (@kamuikatsurgi), Adam Dossa (@adamdossa)
Description: Reprices cold SLOAD/SSTORE and a set of cryptographic precompiles (BN254, BLS12-381, blake2F) for Polygon PoS, activated by the Chicago hard fork.

Status: Final
Type: Core
Date: 2026-05-21
---

## Abstract

This PIP reprices a set of gas costs on Polygon PoS that have proven underpriced relative to their real execution cost. Two classes of operations are repriced:

1. **Cold storage access.** EIP-2929's single `COLD_SLOAD_COST` (2100) is split into two independent constants and both are raised: `COLD_SLOAD_COST` becomes 5460 (2.6×) and a new `COLD_SSTORE_COST` is introduced at 2940 (1.4×). The slot-clear refund is recomputed against the new `COLD_SSTORE_COST` and becomes 3960 (down from 4800).
2. **Cryptographic precompiles.** BN254 add/scalar-mul/pairing, BLS12-381 add/mul/pairing/map (EIP-2537), and the blake2F compression precompile are repriced upward by multipliers ranging from 1.5× (BN254 pairing) to 22× (blake2F `GFROUND`).

All other EIP-2200 / EIP-2929 / EIP-3529 invariants are preserved. In particular, the `cold + (RESET − cold) == 5000` SSTORE invariant continues to hold because the same `COLD_SSTORE_COST` is subtracted from `SSTORE_RESET_GAS`. Activation is consensus-affecting and occurs at the Chicago hard-fork block on each chain.

## Motivation

The gas schedule for cold storage access and for several cryptographic precompiles on Polygon PoS no longer reflects their cost on the network's current hardware and block-time profile. As block times have shortened and L2/zk workloads have pushed BN254 and BLS12-381 precompiles into hot paths, the existing schedule allows individual transactions to consume disproportionate execution time relative to the gas they pay, which both distorts fee markets and raises the worst-case validation time per block.

Cold storage access in particular was sized by EIP-2929 for Ethereum mainnet at the time of the Berlin hard fork. Polygon's higher throughput and shorter block time mean that the marginal real cost of a cold trie lookup, relative to the rest of the block budget, is materially higher than on Ethereum; the same gas no longer buys the same proportion of a block. Splitting the single EIP-2929 constant into `COLD_SLOAD` and `COLD_SSTORE` lets each be sized independently for its actual cost profile.

The precompile multipliers are similarly motivated: pairing checks and large MSMs dominate validation time in proof-heavy blocks, and their existing prices substantially under-charge for that work. The new values bring per-precompile cost back into line with measured execution time on validator hardware.

## Specification

The changes are activated atomically at the **Chicago** hard-fork block. Pre-Chicago behavior is unchanged; from the Chicago block onward the new schedule applies to all transactions.

### Activation

| Network | Activation block | Target time          |
| ------- | ---------------: | -------------------- |
| Amoy    |       `38358000` | 2026-05-14 14:00 UTC |
| Mainnet |       `87218600` | 2026-05-21 14:00 UTC |

### Cold-storage repricing

EIP-2929 defines a single `COLD_SLOAD_COST = 2100` used by both `SLOAD` and the cold branch of `SSTORE`. PIP-88 splits this into two constants:

| Constant                        | Pre-Chicago (EIP-2929) | Post-Chicago (PIP-88) | Multiplier |
| ------------------------------- | ---------------------: | --------------------: | ---------: |
| `COLD_SLOAD_COST`               |                 `2100` |                `5460` |       2.6× |
| `COLD_SSTORE_COST` (new)        |      `2100` (implicit) |                `2940` |       1.4× |
| `SSTORE_CLEARS_SCHEDULE` refund |                 `4800` |                `3960` |     −17.5% |

`SLOAD` charges `COLD_SLOAD_COST` on a cold access and `WARM_STORAGE_READ_COST = 100` on a warm access (unchanged).

`SSTORE` follows the EIP-2200 net-gas-metering state machine, but the cold surcharge and the corresponding subtraction in `SSTORE_RESET_GAS` both reference the new `COLD_SSTORE_COST`:

- Cold surcharge added on first access: `COLD_SSTORE_COST = 2940`.
- `SSTORE_RESET_GAS` effective amount for the existing-slot write branch: `SSTORE_RESET_GAS_EIP2200 − COLD_SSTORE_COST = 5000 − 2940 = 2060`.
- Invariant preserved: `COLD_SSTORE_COST + (SSTORE_RESET_GAS − COLD_SSTORE_COST) == 5000`.
- Slot-clear refund: `SstoreClearsScheduleRefund = SSTORE_RESET_GAS − COLD_SSTORE_COST + ACCESS_LIST_STORAGE_KEY_COST = 5000 − 2940 + 1900 = 3960`.
- The "reset to original existing slot" refund becomes `(SSTORE_RESET_GAS − COLD_SSTORE_COST) − WARM_STORAGE_READ_COST = 2060 − 100 = 1960`.
- The "reset to original inexistent slot" refund (`SSTORE_SET_GAS − WARM_STORAGE_READ_COST = 19900`) is unchanged.

### Precompile repricing

The following precompiles are repriced. Addresses are given in the form they appear in the precompile dispatch table.

| Address | Precompile                            | Pre-Chicago (gas) | Post-Chicago (gas) | Multiplier |
| ------- | ------------------------------------- | ----------------: | -----------------: | ---------: |
| `0x06`  | BN254 G1 add (Istanbul)               |             `150` |              `540` |       3.6× |
| `0x07`  | BN254 G1 scalar-mul (Istanbul)        |            `6000` |            `12600` |       2.1× |
| `0x08`  | BN254 pairing base (Istanbul)         |           `45000` |            `67500` |       1.5× |
| `0x08`  | BN254 pairing per-point (Istanbul)    |           `34000` |            `51000` |       1.5× |
| `0x09`  | blake2F `GFROUND` per round           |               `1` |               `22` |        22× |
| `0x0b`  | BLS12-381 G1 add (EIP-2537)           |             `375` |             `1050` |       2.8× |
| `0x0c`  | BLS12-381 G1 MSM mul (EIP-2537)       |           `12000` |            `73200` |       6.1× |
| `0x0d`  | BLS12-381 G2 add (EIP-2537)           |             `600` |             `1620` |       2.7× |
| `0x0e`  | BLS12-381 G2 MSM mul (EIP-2537)       |           `22500` |           `144000` |       6.4× |
| `0x0f`  | BLS12-381 pairing base (EIP-2537)     |           `37700` |           `109330` |       2.9× |
| `0x0f`  | BLS12-381 pairing per-pair (EIP-2537) |           `32600` |            `94540` |       2.9× |
| `0x10`  | BLS12-381 MapG1 (EIP-2537)            |            `5500` |            `15400` |       2.8× |
| `0x11`  | BLS12-381 MapG2 (EIP-2537)            |           `23800` |            `66640` |       2.8× |

For `blake2F`, total cost is `GFROUND × rounds`, where `rounds` is the big-endian `uint32` parsed from the first four bytes of input (`rounds` is bounded by `uint32`, so multiplication by `GFROUND = 22` cannot overflow `uint64`).

For BLS12-381 G1/G2 MSM, the post-Chicago formula remains `gas = (k × mulGas × discount) / 1000`, with the per-multiplication `mulGas` replaced by the PIP-88 value and the discount table unchanged.

For both BN254 and BLS12-381 pairing, the post-Chicago formula remains `gas = baseGas + (len(input) / pairSize) × perPairGas`, with both `baseGas` and `perPairGas` replaced by their PIP-88 values.

No other precompile semantics, return values, or input encodings are changed. The set of active precompiles at the Chicago address is identical to the LisovoPro set; only their gas costs differ.

## Rationale

**Why split `COLD_SLOAD_COST` and `COLD_SSTORE_COST`.** EIP-2929 reused one constant for both because the underlying access (a trie lookup) is the same. In practice, `SLOAD` and `SSTORE` have very different downstream costs. `SSTORE` additionally dirties state, participates in net-gas metering, and interacts with the refund pool. Splitting the constant allows the two paths to be priced for their actual cost rather than forcing them to share a multiplier.

**Why preserve the `cold + (RESET − cold) == 5000` invariant.** Keeping the SSTORE_RESET total at 5000 means contracts that already account for EIP-2929 / EIP-3529 gas costs continue to see the same total when overwriting an already-warm slot. Only the cold surcharge and the refund pool change; the warm-path behavior matches EIP-3529.

**Why these specific multipliers.** The cold-access and precompile multipliers are sized so that worst-case execution time per unit of gas, measured on representative validator hardware, falls back within the band that other EVM operations occupy. The pairing precompiles, which dominate proof-verification workloads, were the largest outliers. Their 1.5× (BN254) and 2.9× (BLS12-381) multipliers bring per-pairing cost back in line with measured time. The 22× multiplier on `blake2F` reflects that the original `GFROUND = 1` was an extreme under-charge relative to measured per-round cost.

**Why ship in a single hard fork.** Repricing fees in isolated steps would create transient windows in which contracts can be DoS'd by a partially-applied schedule. Activating all repricings atomically at the Chicago block avoids that.

## Backwards Compatibility

This change is consensus-affecting and not backwards compatible. From the Chicago activation block onward, transactions that previously fit within their gas limit may now exceed it if they perform cold storage access or use the repriced precompiles. Pre-Chicago behavior is unchanged.

Contracts and off-chain tooling that hard-code gas estimates for the affected operations (notably L2/zk verifiers that pre-compute BN254 or BLS12-381 verification gas) must be updated. `eth_estimateGas` and `eth_call` automatically reflect the new schedule once nodes are upgraded past the activation block. No state migration, snapshot reformat, or resync is required.

A coordinated upgrade is required: all validators and full nodes must run a client release containing this schedule before the activation block on each network.

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
