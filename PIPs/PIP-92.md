---
PIP: 92
Title: Initial Distribution of the PIP-85 Staker Fee Share via Staking Rewards
Authors: Adam Dossa
Description: Distributes the 27.33M POL staker share accrued under PIP-85 up to block 93,430,949 by transferring it to the L1 StakeManager and raising CHECKPOINT_REWARD from 1 October to 1 December 2026, with no smart-contract changes
Discussion: TBD
Status: Draft
Type: Core
Date: 2026-09-23
---

## Abstract

[PIP-85](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-85.md) allocates 50% of post-commission priority fees to POL stakers, to be paid through merkle claimers on Ethereum. The claimers were not deployed, and the staker share has accumulated undistributed in the PIP-65 fee-collection account since PIP-85 activated at block 85,245,000.

This proposal distributes the accumulated share, **27,334,955.85 POL** as of block [93,430,949](https://polygonscan.com/block/93430949), through the existing staking-reward mechanism and without smart-contract changes. The POL is bridged to Ethereum and transferred to the StakeManager, and `CHECKPOINT_REWARD` is raised from 25,212.79 POL to 64,500 POL between **1 October and 1 December 2026**, lifting the network's annualised gross staking reward rate (total checkpoint rewards over total stake) from about 3.0% to about 7.7% for the window; the additional rewards flow through the normal staking-reward distribution, so the usual proposer-bonus and validator-commission rules apply. Any shortfall is reimbursed from later monthly staker share; any undistributed balance rolls into the next PIP-92 round or into PIP-93, depending on timing.

## Motivation

PIP-85 has applied since April 2026 and the monthly fee-split runs have set aside the staker share each month, but none of it has reached stakers. Distributing POL directly to individual staker addresses through a bespoke claim contract raised legal and compliance considerations that could not be resolved on a timescale compatible with monthly distributions.

The StakeManager's checkpoint rewards already deliver POL to signing validators and their delegators in proportion to stake, through the interfaces they already use, and the reward rate is a governance parameter that has been adjusted three times in the last year under two proposals (PIP-78, PIP-86). Using it distributes the backlog on a fixed timetable with no new code. The permanent, automated mechanism, which will need a StakeManager upgrade, is delivered separately under PIP-93.

## Specification

### 1. Parameters

| Item | Value |
|------|-------|
| Fee-collection account (Polygon PoS) | [`0x7Ee41D8A25641000661B1EF5E6AE8A00400466B0`](https://polygonscan.com/address/0x7Ee41D8A25641000661B1EF5E6AE8A00400466B0) |
| StakeManager proxy (Ethereum) | [`0x5e3Ef299fDDf15eAa0432E6e66473ace8c13D908`](https://etherscan.io/address/0x5e3Ef299fDDf15eAa0432E6e66473ace8c13D908) |
| Governance proxy (Ethereum) | `0x6e7a5820baD6cebA8Ef5ea69c0C92EbbDAc9CE48` |
| POL token (Ethereum) | `0x455e53CBB86018Ac2B8092FdCd39d8444aFFC3F6` |
| Native POL token (Polygon PoS) | `0x0000000000000000000000000000000000001010` |
| Plasma ERC20 predicate (Ethereum) | `0x4EeA1780c06709D7FA0BCaA6D0f1aB29673586c0` |
| WithdrawManager proxy (Ethereum) | `0x2A88696e0fFA76bAA1338F2C74497cC013495922` |
| DepositManager proxy (Ethereum) | `0x401F6c983eA34274ec46f84D70b31C151321188b` |
| Cut-off block (inclusive) | **93,430,949** (2026-09-08 07:10:57 UTC) |
| Distribution window | **2026-10-01 00:00 UTC to 2026-12-01 00:00 UTC** (61 days) |
| `X` (amount to distribute) | **27,334,955.845734223717311595 POL** (`27334955845734223717311595` wei) |
| `R_base` (current `CHECKPOINT_REWARD`, PIP-86) | 25,212.785388127853881278 POL (`25212785388127853881278` wei) |
| `R_window` (elevated `CHECKPOINT_REWARD`) | **64,500 POL** (`64500000000000000000000` wei) |
| `I` (`checkPointBlockInterval`) | 5,120 blocks |

### 2. Amount to distribute

`X` is the sum of `Pool_stakers` (PIP-85) over the five monthly fee-split runs from PIP-85 activation to the cut-off block, computed with the open-source [veblop-fee-split](https://github.com/0xPolygon/veblop-fee-split) tool (`C = 0.26`, `Sf = 0.5`, `Ef = 0.75`). The cut-off coincides with the end of the September run, so no partial-month calculation is needed. Fee amounts are balance differences between a snapshot taken at the block before each range and one taken at its end block, so every block in the table is covered exactly once, except that run 1's opening snapshot was taken at block 85,245,000 itself; the staker share of the fees in that one block, about 8.5 POL, is therefore not in `X` and is carried into the next distribution.

| Run | Period | Start block | End block | Staker pool (POL) |
|-----|--------|-------------|-----------|-------------------|
| 1 | 8 Apr to 8 May 2026 | 85,245,000 | 86,559,900 | 6,238,113.903043866177072908 |
| 2 | 8 May to 8 Jun 2026 | 86,559,901 | 88,144,805 | 5,853,946.973125487851511788 |
| 3 | 8 Jun to 8 Jul 2026 | 88,144,806 | 89,855,909 | 6,289,625.795601788635548228 |
| 4 | 8 Jul to 8 Aug 2026 | 89,855,910 | 91,672,741 | 5,420,186.058746224331859373 |
| 5 | 8 Aug to 8 Sep 2026 | 91,672,742 | 93,430,949 | 3,533,083.115216856721319298 |
| **Total** | | | | **27,334,955.845734223717311595** |

### 3. Transfer to the StakeManager

Native POL is not an asset of the PoS bridge (the RootChainManager has no token mapping for it), so `X` leaves Polygon PoS through the Plasma withdrawal path for the native token:

1. **Burn on Polygon PoS.** The account holding `X` calls `withdraw(X)` on the native token contract `0x0000000000000000000000000000000000001010`, sending `X` POL as the transaction value. This burns the POL and emits a `Withdraw` event.
2. **Exit on Ethereum.** After the burn is included in a checkpoint, the same address that burned calls `startExitWithBurntTokens` on the Plasma ERC20 predicate (`0x4EeA1780c06709D7FA0BCaA6D0f1aB29673586c0`) with the burn receipt proof; the predicate requires the caller to be the burning address. The exit is then processed through `processExits` on the WithdrawManager (`0x2A88696e0fFA76bAA1338F2C74497cC013495922`); with `HALF_EXIT_PERIOD` currently 1 second, it is processable almost immediately. The DepositManager (`0x401F6c983eA34274ec46f84D70b31C151321188b`) pays exits of the native token in POL, so no MATIC-to-POL conversion is needed.
3. **Custody.** The exit must be started by, and pays out to, the Ethereum address equal to the Polygon burning address. The fee-collection account is a Safe deployed at the same address on both networks with the same signers and the same 3-signature threshold, so the exit is started from, and pays to, an account under the same control.
4. **Transfer.** From the exit recipient, `X` POL is transferred to the StakeManager proxy as a plain ERC-20 transfer. The StakeManager pays rewards from its POL balance, so no function call is needed.
5. **Confirmation.** All steps complete, and receipt of `X` on the StakeManager is confirmed on-chain, before the increase transaction in section 5 is executed. All transaction hashes are published.

The transfer only funds the distribution. Claimable rewards are ledger entries written by the checkpoint path, so the parameter change in section 4 is what delivers `X` to stakers. The base emission schedule (PIP-78, PIP-86) is unaffected.

### 4. Elevated reward

`CHECKPOINT_REWARD` is the source of all staking rewards on Polygon PoS. Each checkpoint's reward is split 10% to the checkpoint proposer recorded in the checkpoint data (the proposer bonus, credited to that validator regardless of who sends the Ethereum transaction) and 90% across all signing validators in proportion to stake, with each validator's share divided between its own stake, its commission, and its delegators. Stake behind a validator that did not sign a checkpoint earns nothing for that checkpoint. Raising the parameter therefore raises delegator and validator rewards in the same proportion.

A checkpoint covering `n` blocks pays approximately `CHECKPOINT_REWARD × n / I`, scaled by the share of stake that signed it, and rewards scale linearly with the parameter. Rather than assume a block time and full participation, `R_window` is sized from the reward actually paid at `R_base` over the 14.05 days from 2026-09-09 08:02 to 2026-09-23 09:14 UTC, read from the `NewHeaderBlock` events on the RootChain in Ethereum blocks 25,938,462 to 26,039,262. The 1,197 checkpoints submitted after the first event in that span paid 4,002,567 POL, or 284,880 POL per day (the first event's reward covers blocks produced before the span and is excluded), a gross reward rate of about 3.01% annualised on the 3,451.7M POL staked. For reference, the idealised model of 1.5 s blocks with all stake signing gives 283,644 POL per day, 0.4% lower.

```
extra needed per day  = X / 61                 = 448,114 POL
break-even multiplier = 1 + 448,114 / 284,880  = 2.5730
break-even R_window   = 25,212.79 × 2.5730     = 64,872.27 POL
```

`R_window` is set to **64,500 POL**, rounded down from the break-even value so that the expected outcome is a small surplus of about 257,000 POL (0.9% of `X`) rather than a shortfall.

| | `CHECKPOINT_REWARD` (POL) | Expected reward payout per day (POL) | Annualised gross reward rate on 3,451.7M staked |
|---|---|---|---|
| Baseline (PIP-86) | 25,212.79 | 284,880 | 3.01% |
| Window | 64,500.00 | 728,788 | 7.71% |
| Uplift | +39,287.21 (2.56×) | +443,908 (27.08M over 61 days) | +4.69 pp |

The rates in this table are aggregate gross rates, total checkpoint rewards divided by total stake. The additional rewards are paid through the normal staking-reward distribution, so all of its usual rules apply: the proposer bonus, each validator's commission on its delegators' share, and the exclusion of stake behind validators that did not sign a checkpoint. Individual returns therefore differ from the gross rate in the same way they do for base rewards today. No new POL is issued: the increment is paid from the transferred `X`.

`R_window` is fixed at this value. If the reward rate over the elevated interval differs from the trailing rate, the difference is handled by reconciliation (section 6).

### 5. Timeline

| When | Action | Executed by |
|------|--------|-------------|
| Before 1 Oct 2026 | Publish run outputs backing `X`; withdraw `X` to Ethereum and transfer it to the StakeManager (section 3); confirm receipt on the StakeManager. | Polygon Labs (PIP-65 distribution process) |
| 2026-10-01 00:00 UTC | `updateCheckpointReward(64500000000000000000000)` via Governance. | PoS governance |
| 2026-12-01 00:00 UTC | `updateCheckpointReward(25212785388127853881278)` via Governance, restoring `R_base`. | PoS governance |
| December 2026 | Publish reconciliation. | Polygon Labs |

Each checkpoint is rewarded at the `CHECKPOINT_REWARD` in force when it is submitted, so the two transactions should land as close to the boundaries as operationally feasible. Slippage either way is absorbed by reconciliation, which is defined over the interval actually elevated (section 6).

This proposal assumes `CHECKPOINT_REWARD` is not otherwise changed between the two transactions. If governance approves a different baseline before 1 December 2026, that proposal must state the value the restore transaction uses and the baseline the reconciliation applies to each segment of the interval; absent such a statement, the calldata above stands. If a Polygon PoS block-time change takes effect during the interval, the elevated value and the restore value are rescaled by the same factor in the same governance action, as PIP-86 did for the baseline, so that the daily payout is not changed by the block-time change.

### 6. Reconciliation

The elevated interval is defined by execution, not by the calendar: it runs from the `RewardUpdate` event of the increase transaction to the `RewardUpdate` event of the restore transaction, ordered by Ethereum block number and transaction index. The additional rewards actually distributed are computed over every checkpoint submitted in that interval, including any outside the intended dates:

```
E_actual = sum over checkpoints c in the elevated interval of reward_c × (R_window - R_base) / R_window
```

where `reward_c` is the `reward` field of the checkpoint's `NewHeaderBlock` event. Every term of the reward calculation is proportional to `CHECKPOINT_REWARD`, so this is exact up to integer rounding. If `CHECKPOINT_REWARD` were changed by another governance action during the interval, the calculation is done per segment with the values actually in force and the approved baseline for that segment.

- **Shortfall (`E_actual > X`):** the excess has been paid from the StakeManager's shared reserve, since the contract does not segregate balances. It is reimbursed by transferring the excess to the StakeManager from the staker share ring-fenced for blocks after the cut-off (the runs ending about 8 October 2026 onwards), once the reconciliation is published and before any further PIP-92 round starts; if one run's share is insufficient, following runs are used. The amount reimbursed is deducted from the staker share available to the next distribution round. Base emission and the validator pool are not used.
- **Surplus (`E_actual < X`):** remains in the StakeManager and rolls into the next distribution, either a further PIP-92 round or the PIP-93 mechanism, depending on timing.

At `R_window` the additional reward payout is about 444,000 POL per day, so each day the interval runs longer or shorter than 61 days moves `E_actual` by roughly that amount; a late restore is corrected by executing the restore, not by waiting for the calendar. The reconciliation, the two `RewardUpdate` transaction hashes, and the checkpoint data behind it are published in the discussion thread and the veblop-fee-split repository.

### 7. Later accruals

Only the staker share (`Pool_stakers`) is within the scope of this proposal; amounts that PIP-85 designates for validator distribution or for burning are unaffected and continue to be handled under PIP-85. The PIP-85 formula is unchanged and the staker share continues to be ring-fenced monthly for blocks after the cut-off. Further rounds may be run under this procedure, each with its own cut-off, amount, elevated reward and window announced in advance, until PIP-93 is live. Any remaining ring-fenced staker share, and any undistributed balance under section 6, is then handed over to PIP-93.

## Rationale

**Staking rewards, not claimers.** The reward path already reaches signing validators and their delegators in proportion to stake through existing interfaces, needs no new contract or claim flow, and is the direction PIP-93 is expected to take.

**No contract changes.** The StakeManager has no function that credits a lump sum to stakers; `CHECKPOINT_REWARD` is the only lever without new code. A dedicated distribution function needs an upgrade and audit and belongs with the broader PIP-93 changes.

**A fixed two-month window.** `X` is a fixed stock, so a bounded window makes the drawdown predictable and the end state unambiguous. Two months gives a pronounced but clearly temporary uplift and keeps the elevated parameter in force for the shortest practical period; four months would give about 5.4%, six months about 4.6%.

**Who receives it.** Rewards go to stake present during the window rather than during accrual, validator commission applies, validators' self-stake participates, about 10% of each checkpoint's reward goes to the checkpoint proposer as proposer bonus, and stake behind a validator that misses a checkpoint earns nothing for it. PIP-85 allocated this share to stakers; paying it through the standard distribution means validators receive their usual fee on it, exactly as they do on base rewards, which is how all staker rewards on Polygon PoS are paid. These are accepted trade-offs for a mechanism that needs no new code and no historical snapshots.

## Backwards Compatibility

- No contract upgrades and no Bor or Heimdall changes; the on-chain actions are the Plasma withdrawal of `X`, an ERC-20 transfer into the StakeManager, and two `updateCheckpointReward` calls.
- The PIP-85 formula, validator distributions and base emission target are unchanged; the baseline reward is restored at the end of the window.
- Stakers, validators and staking interfaces see higher per-checkpoint rewards during the window and nothing else.

## Test Cases

- The existing pos-contracts suite covers `updateCheckpointReward` and the reward path (see PIP-86).
- Both governance transactions and a representative checkpoint are rehearsed on a mainnet fork before execution to confirm the per-checkpoint reward at `R_window`, its split between proposer bonus, validator and delegator accounting, and the restore to `R_base`.
- A small test withdrawal over the same Plasma path, from the same account that will burn `X`, is completed end to end before `X` is moved.

## Security Considerations

- **Authorization.** Both changes are `onlyGovernance` and follow the PIP-78 and PIP-86 process. No new privileged surface.
- **Misconfiguration.** A wrong magnitude in `R_window` would misprice the whole window. Values are given in POL and wei, post-state is asserted, and the change is reversible by a further governance call.
- **Late restore.** A delayed 1 December transaction pays out about 444,000 POL per day from the StakeManager's shared reserve. The restore is scheduled in advance, and any excess is reimbursed under section 6.
- **Bridge and custody.** `X` leaves Polygon PoS by a Plasma burn and exit, and the exit pays POL to the Ethereum address matching the burning account, a Safe with the same signers and threshold on both networks. Holding time before the StakeManager transfer is minimised, and all hashes are published.
- **Behavioural effects.** A 2.6× reward rate for two months may attract short-term stake. This introduces no consensus or contract risk; the window is disclosed in advance and should be communicated as a one-off catch-up, not a new base rate.

## References

- [PIP-65: Economic Model for VEBloP Architecture](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-65.md)
- [PIP-85: VEBloP PIP-65 Priority Fee Formula Adjustment](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-85.md)
- [PIP-78: Adjustment of CHECKPOINT_REWARD for Emission Synchronization](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-78.md)
- [PIP-86: Recalibrate CHECKPOINT_REWARD for reduced Polygon block times](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-86.md)
- PIP-93: automated distribution of the staker fee share (forthcoming)
- [veblop-fee-split](https://github.com/0xPolygon/veblop-fee-split): fee-split calculator and distribution records

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
