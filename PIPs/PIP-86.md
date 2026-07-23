---
PIP: 86
Title: Recalibrate CHECKPOINT_REWARD for reduced Polygon block times
Author: Simon Dosch (@simonDos)
Description: >-
  Scale down `CHECKPOINT_REWARD` in lockstep with the planned 1.75s and 1.5s
  block-time reductions to hold annual POL emission at the 1% target reaffirmed
  in PIP-78.
Discussion: TBA
Status: Final
Type: Core
Date: 2026-04-29T00:00:00.000Z
---

  ## Abstract

  Polygon PoS is reducing its average block time in two steps, from approximately 2s to 1.75s, and subsequently to 1.5s. Because checkpoints are submitted every `checkPointBlockInterval = 5120` blocks, a shorter
  block time directly increases the number of checkpoints submitted per year. The per-checkpoint emission parameter `CHECKPOINT_REWARD`, set on the L1 `StakeManager` (`0x5e3Ef299fDDf15eAa0432E6e66473ace8c13D908`)
  and updated through `updateCheckpointReward(uint256)`, must therefore be reduced proportionally.  
  This PIP proposes two new values, each to be applied at the moment its corresponding
  block-time change goes live:  
  **29,414.92 POL** when block time becomes 1.75s, and **25,212.79 POL** when block time becomes 1.5s.

  ## Motivation

  `CHECKPOINT_REWARD` defines the amount of POL distributed to validators per checkpoint. Annual emission is therefore a function of two variables: the per-checkpoint reward and the number of checkpoints produced
  per year. The number of checkpoints per year is itself a function of average block time:

  ```
  checkpoints_per_year = seconds_per_year / (block_time × checkPointBlockInterval)
  ```

  If block time is reduced without adjusting `CHECKPOINT_REWARD`, the validator reward budget increases mechanically. At a 1.5s block time, leaving the
  parameter unchanged would increase emission by roughly 1.4× over the current target.

  The intent of this PIP is therefore to hold annualized emission constant in POL terms while the block-time changes.

  ## Specification

  `StakeManager.updateCheckpointReward(uint256 newReward)` (`onlyGovernance`) is invoked through the `Governance` contract twice, each call timed to coincide with the corresponding block-time change on Polygon PoS:

  1. **At block-time transition to 1.75s (planned for 2026-05-05):**
     - `newReward` = `29414916286149162861491` (29,414.92 POL, 18 decimals)

  2. **At block-time transition to 1.5s (planned for 2026-05-19):**
     - `newReward` = `25212785388127853881278` (25,212.79 POL, 18 decimals)

  Both transactions target the L1 `StakeManager` proxy at `0x5e3Ef299fDDf15eAa0432E6e66473ace8c13D908`. Each successful execution emits `RewardUpdate(newReward, oldReward)` on the `StakingInfo` logger at
  `0xa59C847Bd5aC0172Ff4FE912C5d29E5A71A7512B`.

  No contract upgrades are required. No other parameters (`checkPointBlockInterval`, `proposerBonus`, `dynasty`, etc.) are changed by this PIP.

  ## Rationale

  The current value of `CHECKPOINT_REWARD` is **34,695.98 POL** (set 2026-02-19 by PIP-78, see `RewardUpdate` history). PIP-78 sized that value to deliver the **1% annual emission target** for Year 6 of PIP-26, i.e. **103,530,000 POL/yr**. This PIP preserves that same target across the upcoming block-time reductions.

  Solving directly for the per-checkpoint reward that yields 103,530,000 POL/yr at a given block time:

  ```
  seconds_per_year      = 365 × 24 × 3600 = 31,536,000
  checkpoints_per_year  = seconds_per_year / (block_time × 5,120)
  new_reward            = 103,530,000 / checkpoints_per_year
  ```

  Applying this to the planned transitions:

  | Phase | Block time | Checkpoints / yr | `CHECKPOINT_REWARD` (POL) |
  |-------|-----------:|-----------------:|--------------------------:|
  | Today (PIP-78) | ~2.064s (implied) | 2,983.85 | 34,695.98 |
  | Step 1 (2026-05-05) | **1.75s** | 3,519.64 | **29,414.92** |
  | Step 2 (2026-05-19) | **1.5s**  | 4,106.25 | **25,212.79** |

  Each row holds annualized emission at 103,530,000 POL (≈1% of the ~10.353B POL supply implied by PIP-78's target). The "today" row reflects the block time implied by PIP-78's calibration (target POL/yr ÷ current reward ÷ 5,120 blocks = ~2.064s/block), not a separate measurement.


  ## Backwards Compatibility

  This PIP introduces no backwards-incompatible behavior.
  
  ## Test Cases

  `updateCheckpointReward` and the surrounding reward-distribution path are covered by the existing test suite in `pos-contracts`:

  - `test/units/staking/stakeManager/StakeManager.test.js` exercises governance-gated updates and the resulting reward-calculation behavior.
  - `test/units/root/RootChain.test.js` exercises checkpoint submission and reward emission end-to-end.

  ## Security Considerations

  - **Authorization.** `updateCheckpointReward` is `onlyGovernance`. Execution requires the governance multisig and follows the existing parameter-change process; no new privileged surface is introduced.
  - **Misconfiguration risk.** Setting `CHECKPOINT_REWARD` to an incorrect value (wrong magnitude, wrong decimals) would either over-emit POL or under-pay validators. Mitigations: (a) the values in this PIP are
  stated in both human-readable POL and 18-decimal wei form, (b) the deployer script asserts the post-state matches the intended value, (c) the change is reversible by a subsequent governance call.
  - **Timing / sequencing.** The parameter change should be applied at — or as close as operationally feasible to — the block at which Polygon's average block time actually crosses to the new target. A large lag in
   either direction causes a transient deviation from the target emission rate (over-emission if the parameter lags a faster block time; under-emission if the parameter changes before the block-time reduction takes
   effect).
  - **Validator economics.** The proposed values hold annual POL emission constant in absolute POL terms. Per-checkpoint reward decreases, but validators submit proportionally more checkpoints per unit time, so
  per-unit-time validator rewards are unchanged in expectation.

  ## Copyright

  Copyright and related rights waived via [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
