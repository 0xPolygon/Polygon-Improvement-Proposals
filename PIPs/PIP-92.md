---
PIP: 92
Title: Initial Distribution of the PIP-85 Staker Fee Share via Staking Rewards
Authors: Adam Dossa
Description: Distributes the staker share accrued under PIP-85 by transferring it to the L1 StakeManager and temporarily raising CHECKPOINT_REWARD, with no smart-contract changes; round 1 pays 27.33M POL accrued up to block 93,430,949 from 1 October to 1 December 2026, and round 2 pays the 6.49M POL accrued after that up to block 96,964,517, plus round 1's projected surplus, from 1 December 2026 to 1 February 2027
Discussion: TBD
Status: Draft
Type: Core
Date: 2026-09-23
---

## Abstract

[PIP-85](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-85.md) allocates 50% of post-commission priority fees to POL stakers, to be paid through merkle claimers on Ethereum. The claimers were not deployed, and the staker share has accumulated undistributed in the PIP-65 fee-collection account since PIP-85 activated at block 85,245,000.

This proposal distributes the accumulated share, **27,334,955.85 POL** as of block [93,430,949](https://polygonscan.com/block/93430949), through the existing staking-reward mechanism and without smart-contract changes. The POL is bridged to Ethereum and transferred to the StakeManager, and `CHECKPOINT_REWARD` is raised from 25,212.79 POL to 64,500 POL between **1 October and 1 December 2026**, lifting the network's annualised gross staking reward rate (total checkpoint rewards over total stake) from about 3.0% to about 7.7% for the window; the additional rewards flow through the normal staking-reward distribution, so the usual proposer-bonus and validator-commission rules apply. Any shortfall is reimbursed from later monthly staker share; any undistributed balance rolls into the next PIP-92 round or into the automated mechanism, depending on timing.

A second round (section 8) distributes the **6,485,618.86 POL** staker share accrued after round 1's cut-off up to block [96,964,517](https://polygonscan.com/block/96964517) (including about 8.5 POL carried from round 1), together with the surplus projected from round 1, by setting `CHECKPOINT_REWARD` to **35,000 POL** from **1 December 2026 to 1 February 2027**. On the stake at the time of writing, that gives an annualised gross staking reward rate of about 3.8%, against about 2.7% at the baseline and about 7.0% during round 1. Rounds 1 and 2 are reconciled together at the end of round 2.

## Motivation

PIP-85 has applied since April 2026 and the monthly fee-split runs have set aside the staker share each month, but none of it has reached stakers. Distributing POL directly to individual staker addresses through a bespoke claim contract raised legal and compliance considerations that could not be resolved on a timescale compatible with monthly distributions.

The StakeManager's checkpoint rewards already deliver POL to signing validators and their delegators in proportion to stake, through the interfaces they already use, and the reward rate is a governance parameter that has been adjusted three times in the last year under two proposals (PIP-78, PIP-86). Using it distributes the backlog on a fixed timetable with no new code. The permanent, automated mechanism, which will need a StakeManager upgrade, is to be delivered separately under a forthcoming PIP.

## Specification

Sections 1 to 6 specify round 1. Section 8 specifies round 2, which follows the same procedure.

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
| Distribution window | **2026-10-01 03:00 UTC to 2026-12-01 03:00 UTC** (61 days) |
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

`R_window` is fixed at this value. If the reward rate over the elevated interval differs from the trailing rate, the difference is handled by reconciliation (sections 6 and 8.5).

### 5. Timeline

| When | Action | Executed by |
|------|--------|-------------|
| Before 1 Oct 2026 | Publish run outputs backing `X`; withdraw `X` to Ethereum and transfer it to the StakeManager (section 3); confirm receipt on the StakeManager. Done: burned in Polygon PoS block 94,707,402 ([tx](https://polygonscan.com/tx/0x220e39b50e8ba6c72b2037ecd2f9d3dc27170b2a39ff7cab01fdc5eaf6163878)), transferred to the StakeManager in Ethereum block 26,090,773 ([tx](https://etherscan.io/tx/0x880b531f3fa68e30ba4c2c0a29b8d69f269d633ae4328c64548678ca8c7bff4a)). | Polygon Labs (PIP-65 distribution process) |
| 2026-10-01 03:00 UTC | `updateCheckpointReward(64500000000000000000000)` via Governance. Done: Ethereum block 26,094,638, 2026-10-01 03:00:23 UTC ([tx](https://etherscan.io/tx/0x2f5cad77ffd2601301287b7644cea34fe0d5b5424e620791c2aff83db484ae2c)). | PoS governance |
| 2026-12-01 03:00 UTC | `updateCheckpointReward(35000000000000000000000)` via Governance, ending round 1 and starting round 2 (section 8). | PoS governance |
| December 2026 | Publish the round-1 figures; round 1 is settled with round 2 (section 8.5). | Polygon Labs |

Each checkpoint is rewarded at the `CHECKPOINT_REWARD` in force when it is submitted, so the two transactions should land as close to the boundaries as operationally feasible. Slippage either way is absorbed by reconciliation, which is defined over the interval actually elevated (section 6).

This proposal assumes `CHECKPOINT_REWARD` is not otherwise changed within a round. If governance approves a different baseline before a round ends, that proposal must state the value the transaction ending the round uses and the baseline the reconciliation applies to each segment of the interval; absent such a statement, the calldata in this section and in section 8 stands. If a Polygon PoS block-time change takes effect during a round, the elevated value and the value that ends the round are rescaled by the same factor in the same governance action, as PIP-86 did for the baseline, so that the daily payout is not changed by the block-time change.

### 6. Reconciliation

The elevated interval is defined by execution, not by the calendar: it runs from the `RewardUpdate` event of the increase transaction to the `RewardUpdate` event of the transaction that ends the round, ordered by Ethereum block number and transaction index. For round 1 that is the 1 December 2026 transaction that sets the round-2 value. The additional rewards actually distributed are computed over every checkpoint submitted in that interval, including any outside the intended dates:

```
E_actual = sum over checkpoints c in the elevated interval of reward_c × (R_window - R_base) / R_window
```

where `reward_c` is the `reward` field of the checkpoint's `NewHeaderBlock` event. Every term of the reward calculation is proportional to `CHECKPOINT_REWARD`, so this is exact up to integer rounding. If `CHECKPOINT_REWARD` were changed by another governance action during the interval, the calculation is done per segment with the values actually in force and the approved baseline for that segment.

- **Shortfall (`E_actual > X`):** the excess has been paid from the StakeManager's shared reserve, since the contract does not segregate balances. It is reimbursed by transferring the excess to the StakeManager from staker share ring-fenced for blocks after the cut-off that no round has yet distributed, once the reconciliation is published; if one run's share is insufficient, following runs are used. The amount reimbursed is deducted from the staker share available to the next distribution round. Base emission and the validator pool are not used.
- **Surplus (`E_actual < X`):** remains in the StakeManager and rolls into the next distribution, either a further PIP-92 round or the automated mechanism, depending on timing.

Round 1 ends at the moment round 2 starts, so its result is not known when round 2 is sized. Round 2 is therefore sized to also pay the surplus projected for round 1, and round 1 is settled through the combined reconciliation in section 8.5 instead of on its own. The round-1 figures are still published in December 2026, for information.

At `R_window` the additional reward payout is about 442,000 POL per day as observed (section 8.4), so each day the interval runs longer or shorter than 61 days moves `E_actual` by roughly that amount; a late transaction ending the round is corrected by executing it, not by waiting for the calendar. The reconciliation, the two `RewardUpdate` transaction hashes, and the checkpoint data behind it are published in the discussion thread and the veblop-fee-split repository.

### 7. Later accruals

Only the staker share (`Pool_stakers`) is within the scope of this proposal; amounts that PIP-85 designates for validator distribution or for burning are unaffected and continue to be handled under PIP-85. The PIP-85 formula is unchanged and the staker share continues to be ring-fenced monthly for blocks after the cut-off. Further rounds may be run under this procedure, each with its own cut-off, amount, elevated reward and window announced in advance, until the automated mechanism is live. Any remaining ring-fenced staker share, and any undistributed balance under section 6 or section 8.5, is then handed over to that mechanism.

### 8. Round 2

#### 8.1 Parameters

Addresses, `R_base` and `I` are as in section 1.

| Item | Value |
|------|-------|
| Cut-off block (inclusive) | **96,964,517** (2026-11-08 15:37:12 UTC) |
| Distribution window | **2026-12-01 03:00 UTC to 2027-02-01 03:00 UTC** (62 days) |
| `X2` (amount to transfer) | **6,485,618.860774875112560337 POL** (`6485618860774875112560337` wei) |
| `S1` (projected round-1 surplus, already in the StakeManager) | about 373,112 POL |
| `R_window2` (elevated `CHECKPOINT_REWARD`) | **35,000 POL** (`35000000000000000000000` wei) |

#### 8.2 Amount to distribute

`X2` is the sum of `Pool_stakers` over the two monthly fee-split runs after the round-1 cut-off, computed with the same tool and parameters as in section 2, plus the staker share of block 85,245,000 that section 2 carried forward. Each run's opening snapshot is taken at the previous run's end block, so the ranges are contiguous with round 1 and with each other. The carried amount is 37% (`(1 - C) × Sf`) of the 23.012302115308431336 POL that the fee-collection account received in block 85,245,000, rounded down to the wei.

| Run | Period | Start block | End block | Staker pool (POL) |
|-----|--------|-------------|-----------|-------------------|
| 6 | 8 Sep to 8 Oct 2026 | 93,430,950 | 95,157,157 | 3,155,355.255119486947700544 |
| 7 | 8 Oct to 8 Nov 2026 | 95,157,158 | 96,964,517 | 3,330,255.091103605500740199 |
| Carried from round 1 | block 85,245,000 (section 2) | 85,245,000 | 85,245,000 | 8.514551782664119594 |
| **Total** | | | | **6,485,618.860774875112560337** |

Both runs were checked against the chain, using the fee-collection account's balance at each end block plus every POL outflow from it in between, found by tracing each Safe execution. Run 7 matches to within 20 wei. Run 6 is 0.007266 POL below the reconstruction, and round 1's runs together are 0.039604 POL below it, because some earlier outflows from the account were entered into the tool's distribution list with rounded amounts, or without a 0.019967 POL side transfer made in the same transaction. Later runs detect outflows on-chain and are not affected. The 0.046871 POL difference is carried into the next distribution.

#### 8.3 Transfer

`X2` is moved to the StakeManager by the procedure in section 3, and its receipt confirmed on-chain, before the 1 December 2026 transaction. `S1` is not transferred: it is the part of `X` that round 1 is expected not to pay out, and it is already in the StakeManager.

#### 8.4 Elevated reward

`R_window2` is sized from round 1's own payout instead of the section-4 calibration. From the first checkpoint after the 1 October increase to the checkpoint submitted at 2026-11-17 13:15 UTC (checkpoints 112,919 to 117,129 in Ethereum blocks 26,094,691 to 26,434,999, 47.42 days, with the first checkpoint's reward excluded from the rate as in section 4), checkpoints paid 725,636 POL per day at 64,500 POL. That is 11.2502 POL per day for each POL of `CHECKPOINT_REWARD`, or 283,648 POL per day at `R_base`, 0.4% below the section-4 calibration.

By that checkpoint round 1 had paid `E_actual` = 20,962,896 POL (section 6). At 441,988 POL per day for the remaining 13.57 days to 2026-12-01 03:00 UTC, it is expected to end at about 26,961,843 POL, leaving `S1` ≈ 373,112 POL (1.36% of `X`). Round 2 is sized to pay `X2 + S1`:

```
target                 = X2 + S1                           = 6,858,731 POL
extra needed per day   = target / 62                       = 110,624.7 POL
break-even R_window2   = 25,212.79 + 110,624.7 / 11.2502   = 35,045.94 POL
```

`R_window2` is set to **35,000 POL**, rounded down from the break-even value so that the expected outcome is a small surplus of about 32,000 POL (0.47% of the target) rather than a shortfall.

| | `CHECKPOINT_REWARD` (POL) | Expected reward payout per day (POL) | Annualised gross reward rate on 3,808.4M staked |
|---|---|---|---|
| Baseline (PIP-86) | 25,212.79 | 283,648 | 2.72% |
| Round 2 | 35,000.00 | 393,756 | 3.77% |
| Uplift | +9,787.21 (1.39×) | +110,108 (6.83M over 62 days) | +1.06 pp |

The stake figure is the total recorded by the StakeManager on 17 November 2026, up from 3,451.7M in section 4, so the same `CHECKPOINT_REWARD` now gives a lower rate than it did then. As in section 4, these are aggregate gross rates, the additional rewards follow the normal staking-reward distribution, and no new POL is issued. `R_window2` is fixed at this value, and any difference from the expected payout is handled by the combined reconciliation.

#### 8.5 Timeline and combined reconciliation

| When | Action | Executed by |
|------|--------|-------------|
| Before 2026-12-01 03:00 UTC | Publish run outputs backing `X2`; withdraw `X2` to Ethereum and transfer it to the StakeManager (section 3); confirm receipt on the StakeManager. | Polygon Labs (PIP-65 distribution process) |
| 2026-12-01 03:00 UTC | `updateCheckpointReward(35000000000000000000000)` via Governance, ending round 1 and starting round 2. | PoS governance |
| December 2026 | Publish the round-1 figures (section 6), for information. | Polygon Labs |
| 2027-02-01 03:00 UTC | `updateCheckpointReward(25212785388127853881278)` via Governance, restoring `R_base`. | PoS governance |
| February 2027 | Publish the combined reconciliation. | Polygon Labs |

The 1 December transaction moves the parameter straight from the round-1 value to the round-2 value; there is no intermediate restore to `R_base`. Round 2's interval runs from that transaction's `RewardUpdate` event to the `RewardUpdate` event of the 1 February 2027 restore, defined as in section 6. Rounds 1 and 2 are then reconciled together:

```
E_1     = sum over checkpoints c in round 1's interval of reward_c × (64,500 - R_base) / 64,500
E_2     = sum over checkpoints c in round 2's interval of reward_c × (35,000 - R_base) / 35,000
balance = (X + X2) - (E_1 + E_2)
```

A negative balance is a shortfall and is reimbursed as in section 6, from the staker share ring-fenced for blocks after 96,964,517. A positive balance is a surplus and rolls into the next distribution. Because the intervals are defined by execution, timing slippage is absorbed automatically: each hour the 1 December transaction lands late pays about 13,800 POL more in total, and each day the 1 February restore lands late pays about 110,000 POL more.

## Rationale

**Staking rewards, not claimers.** The reward path already reaches signing validators and their delegators in proportion to stake through existing interfaces, needs no new contract or claim flow, and is the direction the automated mechanism is expected to take.

**No contract changes.** The StakeManager has no function that credits a lump sum to stakers; `CHECKPOINT_REWARD` is the only lever without new code. A dedicated distribution function needs an upgrade and audit and belongs with the automated mechanism.

**A fixed two-month window.** `X` is a fixed stock, so a bounded window makes the drawdown predictable and the end state unambiguous. Two months gives a pronounced but clearly temporary uplift and keeps the elevated parameter in force for the shortest practical period; four months would give about 5.4%, six months about 4.6%.

**Who receives it.** Rewards go to stake present during the window rather than during accrual, validator commission applies, validators' self-stake participates, about 10% of each checkpoint's reward goes to the checkpoint proposer as proposer bonus, and stake behind a validator that misses a checkpoint earns nothing for it. PIP-85 allocated this share to stakers; paying it through the standard distribution means validators receive their usual fee on it, exactly as they do on base rewards, which is how all staker rewards on Polygon PoS are paid. These are accepted trade-offs for a mechanism that needs no new code and no historical snapshots.

## Backwards Compatibility

- No contract upgrades and no Bor or Heimdall changes; the on-chain actions in each round are a Plasma withdrawal, an ERC-20 transfer into the StakeManager, and `updateCheckpointReward` calls.
- The PIP-85 formula, validator distributions and base emission target are unchanged; the baseline reward is restored at the end of the last round.
- Stakers, validators and staking interfaces see higher per-checkpoint rewards during each round's window and nothing else.

## Test Cases

- The existing pos-contracts suite covers `updateCheckpointReward` and the reward path (see PIP-86).
- Each round's governance transactions and a representative checkpoint are rehearsed on a mainnet fork before execution to confirm the per-checkpoint reward at the elevated value, its split between proposer bonus, validator and delegator accounting, and the move to the next value.
- A small test withdrawal over the same Plasma path, from the same account that will burn `X`, is completed end to end before `X` is moved. Round 2 uses the same path and account, which round 1 has exercised end to end.

## Security Considerations

- **Authorization.** All `updateCheckpointReward` calls are `onlyGovernance` and follow the PIP-78 and PIP-86 process. No new privileged surface.
- **Misconfiguration.** A wrong magnitude in `R_window` would misprice the whole window. Values are given in POL and wei, post-state is asserted, and the change is reversible by a further governance call.
- **Late transactions.** A delayed 1 December 2026 transaction keeps 64,500 POL in force, paying about 332,000 POL per day more than round 2 would; a delayed 1 February 2027 restore pays about 110,000 POL per day. Both are scheduled in advance, and any excess is settled by the combined reconciliation (section 8.5).
- **Bridge and custody.** `X` leaves Polygon PoS by a Plasma burn and exit, and the exit pays POL to the Ethereum address matching the burning account, a Safe with the same signers and threshold on both networks. Holding time before the StakeManager transfer is minimised, and all hashes are published.
- **Behavioural effects.** A 2.6× reward rate in round 1 and a 1.39× rate in round 2 may attract short-term stake. This introduces no consensus or contract risk; each window is disclosed in advance and should be communicated as a one-off catch-up, not a new base rate, including the step down from round 1's rate to round 2's on 1 December 2026.

## References

- [PIP-65: Economic Model for VEBloP Architecture](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-65.md)
- [PIP-85: VEBloP PIP-65 Priority Fee Formula Adjustment](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-85.md)
- [PIP-78: Adjustment of CHECKPOINT_REWARD for Emission Synchronization](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-78.md)
- [PIP-86: Recalibrate CHECKPOINT_REWARD for reduced Polygon block times](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-86.md)
- Automated distribution of the staker fee share (forthcoming PIP)
- [veblop-fee-split](https://github.com/0xPolygon/veblop-fee-split): fee-split calculator and distribution records

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
