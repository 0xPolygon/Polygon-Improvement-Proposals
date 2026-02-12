---
PIP: 78  
Title: Adjustment of CHECKPOINT_REWARD for Emission Synchronization
Authors: Parvez Shaikh 
Discussion: https://forum.polygon.technology/t/pip-78-adjustment-of-checkpoint-reward-for-emission-synchronization/21710
Status: Draft
Type: Contracts  
Date: 2026-02-01
---

## Abstract

This proposal reduces the `CHECKPOINT_REWARD` parameter in the `StakeManager` contract by 6.5% to restore alignment between actual validator reward emissions and the 1% annual target defined in PIP-26. Empirical analysis of checkpoint events since the July 2025 emission adjustment shows that rewards have exceeded the intended rate by approximately 7%, primarily due to higher-than-anticipated checkpoint frequency following Polygon PoS network speed improvements.

The proposal adopts a forward-looking correction that restores emission accuracy from the point of implementation onward, without attempting to retroactively offset past over-distribution, thereby minimizing disruption to validator economics and network stability.

## Motivation

### Background: PIP-26 Emission Schedule

PIP-26 (“Transition from MATIC to POL Validator Rewards”) defined a fixed emission schedule for validator rewards, including the following relevant periods:

| Period | Timeframe           | Annual Target (POL) | Annual Rate |
| ------ | ------------------- | ------------------- | ----------- |
| Year 5 | Jun 2024 – Jun 2025 | 150,300,000         | 1.5%        |
| Year 6 | Jun 2025 – Jun 2026 | 103,530,000         | 1.0%        |

The Year 6 emission target of 103,530,000 POL was calculated based on assumptions regarding checkpoint frequency and network parameters prevailing at the time of PIP-26 approval.

### Problem Statement: Emission–Distribution Desynchronization

Following the July 2025 transition to the Year 6 emission rate, observed reward distribution has exceeded projections. Analysis of checkpoint events from block 22,885,942 through block 24,219,582 (January 12, 2026) shows the following:

* **Total checkpoints:** 10,603
* **Total rewards distributed:** 56,193,648.45 POL
* **Expected distribution (pro rata):** 52,538,717 POL
* **Over-delivery:** 3,654,931 POL (~7%)
* **Current daily emission rate:** ~303,000 POL/day
* **Target daily emission rate:** 283,644 POL/day
* **Current `CHECKPOINT_REWARD`:** 37,108,000,000,000,000,000,000 wei

### Root Cause: Increased Checkpoint Frequency

Polygon PoS network speed improvements have resulted in a higher-than-anticipated checkpoint frequency:

* **Average checkpoints per day:** ~57.3
* **Average reward per checkpoint:** ~5,300 POL
* **Resulting daily emission:** ~303,000 POL/day

This checkpoint frequency materially exceeds the assumptions underpinning PIP-26, resulting in approximately 7% higher emissions than intended.

### Rationale Against Retroactive Compensation

Attempting to strictly reconcile total Year 6 emissions with the original PIP-26 target would require a materially larger reduction in rewards over the remaining period. Such an approach risks validator churn, negative community sentiment, and degraded network security. This proposal instead accepts historical variance and prioritizes restoring correct emission behavior on a forward-looking basis.

## Specification

### Proposed Change

Reduce the `CHECKPOINT_REWARD` parameter in the `StakeManager` contract by **6.5%**, with an optional **7% reduction** presented as a conservative alternative.

### Technical Details

**Current State (post–July 2025 adjustment):**

* `CHECKPOINT_REWARD`: 37,108,000,000,000,000,000,000 wei (37.108e21)
* Average reward per checkpoint: ~5,300 POL
* Checkpoint frequency: ~57/day
* Effective daily emission: ~303,000 POL/day

**Proposed State (6.5% reduction):**

* `CHECKPOINT_REWARD`: 34,695,980,000,000,000,000,000 wei (34.696e21)
* Target reward per checkpoint: ~4,958 POL
* Expected daily emission: ~283,644 POL/day
* Projected annual emission: ~103,530,000 POL

### Implementation

The adjustment should be executed using the existing administrative mechanism in the `StakeManager` contract:

```solidity
// Current
uint256 constant CURRENT = 37108000000000000000000;

// Proposed (6.5% reduction)
uint256 constant PROPOSED = 34695980000000000000000;

// Alternative (7% reduction)
uint256 constant ALTERNATIVE = 34510440000000000000000;

stakeManager.updateCheckpointReward(PROPOSED);
```

### Calculation Methodology

* Current daily rate: 303,376 POL/day
* Target daily rate: 283,644 POL/day
* Required reduction: (303,376 − 283,644) / 303,376 ≈ **6.50%**
* New `CHECKPOINT_REWARD`: 37.108e21 × 0.935 = 34.69598e21 wei

## Rationale

### Choice of 6.5% Reduction

The proposed reduction is directly derived from observed emission data and is sufficient to restore alignment with PIP-26 targets without overshooting or introducing unnecessary economic disruption.

### Expected Year 6 Emissions

This proposal accepts historical over-distribution and does not attempt to retroactively correct it. As a result:

| Component                                 | POL              |
| ----------------------------------------- | ---------------- |
| Distributed (Jul 10, 2025 – Jan 12, 2026) | 56,193,648       |
| Projected remaining (post-adjustment)     | ~50,489,000      |
| **Expected Year 6 total**                 | **~106,700,000** |
| PIP-26 target                             | 103,530,000      |
| Variance                                  | +3.1%            |

This one-time variance does not compound into future periods and preserves validator stability.

### Alternative Considered

A stricter reduction of ~14–15% would be required to exactly meet the original Year 6 target. This approach is not recommended due to heightened risk to validator participation and network security.

### Alignment with PIP-26 Intent

This proposal preserves the intent of PIP-26 by:

1. Maintaining the 1% annual emission rate on a forward-looking basis
2. Avoiding changes to emission logic or contract architecture
3. Operating within existing governance processes
4. Minimizing disruption to validator incentives

## Backwards Compatibility

This proposal introduces no backwards incompatibilities:

* No changes to contract interfaces or ABIs
* No changes to checkpoint submission or validation logic
* No impact on existing integrations

The only observable effect is a reduced reward per checkpoint, consistent with prior parameter updates.

## Security Considerations

* The proposed reduction is modest and should not materially affect validator participation
* No new attack vectors are introduced, as this is a parameter adjustment only
* The change should follow standard governance approval procedures
* Post-deployment monitoring is recommended to verify emission alignment

## Implementation Timeline

| Phase | Action                         | Duration      |
| ----- | ------------------------------ | ------------- |
| 1     | Community discussion           | 2 weeks       |
| 2     | Governance vote                | 1 week        |
| 3     | Implementation and testing     | 1 week        |
| 4     | Mainnet deployment             | Upon approval |
| 5     | Post-implementation monitoring | Ongoing       |

## Monitoring and Future Adjustments

To prevent future desynchronization, the following measures are recommended:

* Automated monitoring of checkpoint counts and reward distribution
* Quarterly emission alignment reviews
* Public dashboards tracking cumulative emissions versus targets
* Parameter adjustment proposals if deviation exceeds 2%

## References

* PIP-26: Transition from MATIC to POL Validator Rewards
* StakeManager contract update PR
* MAINNET POL emissions and staking discussion
* Polygon network speed improvements announcement (September 2025)
* On-chain analysis of `validatorNewHeaderBlock` events (blocks 22,885,942–24,219,582)

## Appendix A: Data Analysis Summary

(See original analysis tables and metrics.)

## Appendix B: Recommended `CHECKPOINT_REWARD` Values

| Reduction | New Value (wei)                    | Notation      |
| --------- | ---------------------------------- | ------------- |
| 5.0%      | 35,252,600,000,000,000,000,000     | 35.253e21     |
| 6.0%      | 34,881,520,000,000,000,000,000     | 34.882e21     |
| **6.5%**  | **34,695,980,000,000,000,000,000** | **34.696e21** |
| 7.0%      | 34,510,440,000,000,000,000,000     | 34.510e21     |
| 7.5%      | 34,324,900,000,000,000,000,000     | 34.325e21     |

## Copyright

All Polygon Improvement Proposals are released under the CC0 1.0 Universal license.
