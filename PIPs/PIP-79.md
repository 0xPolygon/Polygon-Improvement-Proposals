---
PIP: 79
Title: Bounded-Range Validation for Configurable EIP-1559 Parameters
Description: Replace deterministic baseFee validation with boundary-based validation to enable producer-driven fee tuning
Author: Lucca Martins (@lucca30)
Discussion: https://forum.polygon.technology/t/pip-79-bounded-range-validation-for-configurable-eip-1559-parameters/21711
Status: Draft
Type: Core
Date: 2026-02-04
---

## Abstract

Today, Polygon PoS validates EIP-1559 base fees using **deterministic calculation**: validators compute the expected `baseFee` from the parent block using hardcoded parameters (`baseFeeChangeDenominator` and `targetGas`) and reject blocks if the header's `baseFee` doesn't exactly match. This requires hardforks to tune fee market behavior, as parameter changes break consensus.

This PIP proposes a **consensus-breaking change** that replaces deterministic baseFee validation with **boundary validation**: validators accept blocks if the declared `baseFee` is within **±5% of the parent block's baseFee**, rather than requiring an exact calculated match. This allows block producers to **set baseFee within bounded ranges**, enabling gradual, producer-driven adjustment of fee market dynamics without future hardforks.

This proposal **requires a scheduled hardfork** to activate, changes block validation rules from exact-match to boundary-based, preserves consensus safety through bounded rate limits (±5%), and mirrors the existing gas limit governance model while providing faster response to market conditions within a safe envelope.

## Motivation

* **Operational flexibility:** Current EIP-1559 parameters are effectively hardcoded. In chaotic market conditions or when fee dynamics need tuning, the network must wait for a hardfork (slow, coordination-heavy process).
* **Parallel with gas limit governance:** Gas limit already allows producer-proposed adjustments within bounded deltas. EIP-1559 parameters deserve the same flexibility for responsive fee market tuning.
* **Controlled parameter evolution:** A ±5% per-block bound enables gradual steering toward desired fee behavior without enabling extreme swings that could destabilize the fee market.
* **Reduced hardfork dependency:** Teams can respond to empirical fee market behavior (e.g., base fee volatility, network congestion patterns) through social consensus among producers rather than protocol upgrades.

### Historical parameter changes

Polygon PoS has adjusted these EIP-1559 parameters through hardforks to tune fee market behavior:

| Hardfork          | `baseFeeChangeDenominator` | `targetGas` (% of gasLimit) | Fee Responsiveness |
|-------------------|----------------------------|-----------------------------|--------------------|
| **Initial**       | 8                          | 50%                         | High (fast adjust) |
| **Delhi**         | 16                         | 50%                         | Medium             |
| **Bhilai**        | 64                         | 50%                         | Low (stable fees)  |
| **Dandeli**       | 64                         | 65%                         | Low (stable fees)  |
| **This PIP**      | Producer-configurable      | Producer-configurable       | Tunable            |

Each hardfork required months of coordination and deployment. This PIP enables **continuous, bounded adjustment** without hardforks, allowing the network to respond to empirical fee market conditions within a safe envelope.

## Specification

> **IMPORTANT:** This proposal introduces **consensus-breaking changes** to block validation rules that **MUST** be activated via a scheduled network hardfork. Pre-fork blocks use **deterministic baseFee validation** (exact match). Post-fork blocks use **boundary baseFee validation** (±5% range). Mixing validation modes will cause consensus failures.

### 1) Validation mode change

**Pre-fork (deterministic validation):**

Validators compute the expected `baseFee` using hardcoded parameters and the EIP-1559 formula, then reject blocks if `header.baseFee ≠ expectedBaseFee` (exact match required).

**Post-fork (boundary validation):**

Validators check that the block's declared `baseFee` is within **±5% of the parent block's baseFee**, rather than computing an exact expected value. Blocks are accepted if the boundary constraint is satisfied, regardless of the specific calculation method the producer used.

**Rationale:** This shifts validation from "exact match" to "bounded range," enabling producers to tune fee dynamics within safe limits without breaking consensus.

### 2) Boundary validation rules (consensus-critical)

During post-fork block verification, nodes **MUST** enforce the following boundary constraints on the child block's `baseFee`:

#### a) Base fee boundary check (±5%)

Let `B_parent` be the parent block's `baseFee`, and `B_child` be the child block's declared `baseFee`.

**Integer bounds computation:**

* Lower bound: `B_parent * 95 / 100` (floor division)
* Upper bound: `B_parent * 105 / 100` (floor division)

**Validity:**

* `B_child` **MUST** satisfy: `B_parent * 95 / 100 ≤ B_child ≤ B_parent * 105 / 100`

> **Rounding:** Floor division ensures deterministic integer arithmetic across all clients. The 95/100 and 105/100 multipliers are computed before division to preserve precision.

**Example:**

* If `B_parent = 100 gwei`, then `B_child` must be in `[95 gwei, 105 gwei]`
* If `B_parent = 1000 gwei`, then `B_child` must be in `[950 gwei, 1050 gwei]`

#### b) Minimum safety bound

To prevent degenerate base fee values:

* `B_child` **MUST** be ≥ 1 (minimum 1 wei)

#### c) Rejection rule

A block is **invalid** if any of the following holds:

1. The declared `baseFee` violates the ±5% boundary relative to the parent's `baseFee`.
2. The declared `baseFee` is < 1 wei.
3. The `baseFee` field is missing (must exist for EIP-1559 blocks).

### 3) Producer base fee setting

**Producer flexibility:**

Block producers **MAY** set the block's `baseFee` to any value within the ±5% boundary of the parent block's `baseFee`. Producers are **not required** to use a specific calculation formula, but they **MUST** stay within the consensus-enforced boundaries.

**Reference calculation (optional guidance):**

Producers **MAY** use the standard EIP-1559 formula as a starting point, then adjust within boundaries based on observed network conditions:

```
parentGasUsed = parent.gasUsed
parentBaseFee = parent.baseFee
targetGas = <producer-configured value>  // e.g., gasLimit * 65 / 100
denominator = <producer-configured value>  // e.g., 64

// Standard EIP-1559 formula
if parentGasUsed == targetGas:
    suggestedBaseFee = parentBaseFee
elif parentGasUsed > targetGas:
    gasUsedDelta = parentGasUsed - targetGas
    baseFeeIncrement = parentBaseFee * gasUsedDelta / targetGas / denominator
    suggestedBaseFee = parentBaseFee + max(1, baseFeeIncrement)
else:
    gasUsedDelta = targetGas - parentGasUsed
    baseFeeDecrement = parentBaseFee * gasUsedDelta / targetGas / denominator
    suggestedBaseFee = max(1, parentBaseFee - baseFeeDecrement)

// Ensure within consensus boundaries
lowerBound = parentBaseFee * 95 / 100
upperBound = parentBaseFee * 105 / 100
finalBaseFee = clamp(suggestedBaseFee, lowerBound, upperBound)
```

**Key points:**

* Producers can tune parameters (denominator, targetGas) **off-chain** without consensus changes.
* The ±5% boundary provides safety while allowing gradual fee market tuning.
* Producers can respond to network conditions (congestion, volatility) by adjusting where they set baseFee within the allowed range.

### Test vectors (mandatory)

Clients **MUST** pass consensus tests including:

1. **Boundary cases (baseFee validation):**
   * Parent baseFee = 100 gwei, child baseFee = 95 gwei (exactly -5%, valid).
   * Parent baseFee = 100 gwei, child baseFee = 105 gwei (exactly +5%, valid).
   * Parent baseFee = 100 gwei, child baseFee = 94 gwei (< -5%, invalid).
   * Parent baseFee = 100 gwei, child baseFee = 106 gwei (> +5%, invalid).

2. **Rounding edge cases:**
   * Parent baseFee = 1 wei, child baseFee must be in [floor(1*95/100), floor(1*105/100)] = [0, 1] but ≥ 1, so child must be 1 (valid).
   * Parent baseFee = 100 wei, child baseFee in [95, 105] wei (valid range).
   * Parent baseFee = 1000 gwei, child baseFee in [950 gwei, 1050 gwei] (valid range).

3. **Fork activation:**
   * Pre-fork block with baseFee outside ±5% but matching calculated value (valid pre-fork, would be invalid post-fork).
   * Post-fork block with baseFee within ±5% (valid post-fork, might be invalid pre-fork).

4. **Minimum baseFee:**
   * Child baseFee = 0 (invalid, minimum is 1 wei).
   * Child baseFee = 1 (valid if within ±5% of parent).

5. **Invalid blocks:**
   * Block with baseFee < parent baseFee * 95 / 100 (rejected).
   * Block with baseFee > parent baseFee * 105 / 100 (rejected).
   * Block with baseFee = 0 (rejected).

### RPC & observability

* **RPC exposure:** No changes needed. 
* **Monitoring:** Node operators should monitor baseFee volatility and log when baseFee changes approach the ±5% boundaries. Alerts can signal aggressive producer behavior or market instability.
* **Observability tools:** Block explorers may want to display baseFee as a percentage change from parent to highlight producer fee-setting behavior.

### Migration path

* **Activation block:** At hardfork activation, validation switches from deterministic to boundary-based. The first post-fork block's baseFee is typically set to match the calculated value from pre-fork logic to ensure smooth transition, but any value within ±5% of the pre-fork block is valid.
* **Pre-fork sync:** Clients syncing pre-fork history continue using deterministic validation (exact baseFee match).
* **Post-fork sync:** Clients syncing post-fork history use boundary validation (±5% check).

## References

* **EIP-1559:** Fee market change for ETH 1.0 chain — [https://eips.ethereum.org/EIPS/eip-1559](https://eips.ethereum.org/EIPS/eip-1559)

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
