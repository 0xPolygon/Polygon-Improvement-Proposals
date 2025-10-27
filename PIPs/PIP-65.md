---
PIP: 65
Title: Economic Model for VEBloP Architecture
Author: Jerry Chen (fchen@polygon.technology)
Description: Proposes an economic model and fee redistribution mechanism for the Validator-Elected Block Producer (VEBloP) architecture.
Discussion: https://forum.polygon.technology/t/pip-65-economic-model-for-veblop-architecture/20933
Status: Final
Type: Core
Date: 2025-05-08
---


## Abstract

This proposal defines an economic model designed to function alongside the Validator-Elected Block Producer (VEBloP) architecture proposed in PIP-64. Recognizing that VEBloP alters how transaction fees accrue, this PIP introduces a fee redistribution mechanism. The goal is to ensure continued economic viability for validators who perform crucial verification duties but no longer directly produce blocks, thereby maintaining the security and decentralization of the Polygon PoS network. The core proposal involves distributing network fees between the active block producer and the validator set.

## Motivation

The introduction of the Validator-Elected Block Producer (VEBloP) architecture (detailed in PIP-64) focuses block production on specialized validators with the appropriate hardware requirements to enhance throughput and finality. A direct consequence is that transaction fees, traditionally collected by the producer of each block, would accrue solely to the single active block producer.

While VEBloP lowers hardware costs for non-producing validators through stateless validation, the loss of direct transaction fee revenue could negatively impact their economic incentive to participate in the network. Maintaining a robust, decentralized set of validators is critical for network security and integrity.

Therefore, a revised economic model is necessary to:

1. **Compensate Validators:** Provide validators with fair rewards for their essential role in block verification, consensus participation, and data availability, even without producing blocks.
2. **Reward Block Producers:** Provide a specific incentive for the entity undertaking the specialized, potentially higher-cost role of block production.
3. **Maintain Network Security:** Align economic incentives with network health and security contributions (i.e., stake).
4. **Align Validator Incentives With Network Growth:** Ensure that all validators are economically aligned with increasing the number of transactions (and associated fees) on the Polygon chain.

This proposal addresses these needs by explicitly defining how network fees should be distributed in the VEBloP era.

## Specification

The proposed economic model introduces a mechanism for collecting and redistributing network fees generated on the Polygon PoS chain under the VEBloP architecture, while also incentivizing active validator participation.

1. **Fee Pooling:** All relevant network fees (primarily transaction fees and potentially MEV fees) generated within a given period (e.g., per checkpoint) will be directed to a dedicated smart contract system.
2. **Distribution Mechanism:** The pooled fees within the smart contract will be programmatically divided based on protocol parameters:
    - **Total Fee:** Total fee (`T`) collected in a checkpoint (Transaction Fees + MEV Fees)
    - **Block Producer Commission Pool Calculation:** An overall commission rate (`C`) determines the total pool allocated to block producers. (`CommissionPool = T * C`)
    - **Producer Commission Distribution:** This `CommissionPool` is further divided to reward individual producers (`p`) based on two factors:
        - An 'Equal Share' component (`CommissionPool * E / Np` where `E` is the equal share factor and `Np` is the number of active producers) distributed evenly among all designated producers for the period.
        - A 'Proportional Share' component (`CommissionPool * P * (Bp / B_total)` where `P` is the proportional share factor, `Bp` is blocks produced by `p`, and `B_total` is total blocks) distributed based on the number of blocks each producer contributed.
        - The sum of these components forms the commission reward (`Rp_commission`) for producer `p`. (Note: The producer's *total* reward also includes any validator rewards `Rv_bp` earned separately if they participate in validation).
    - **Validator Allocation Pool:** The remaining portion of total fees forms the pool initially available for validator distribution (`Pool_validators = T * (1 - C)`).
3. **Validator Reward Calculation & Adjustment:** Each validator's potential reward from the validator allocation pool is calculated based on their proportional stake. This potential reward is then adjusted based on performance (`Pv`):
    - The `Pool_validators` is distributed among all participating validators. Each validator `v`'s share is determined by their "performance-weighted stake." This is calculated based on their actual stake (`Sv`) multiplied by their performance score (`Pv`).
    - Validators with higher performance scores and/or higher stakes will receive a proportionally larger share of the `Pool_validators`. Conversely, if a validator's performance (`Pv`) is low (e.g., due to missed participation or incorrect votes), their share of the pool will be correspondingly reduced. This effectively means that the portion of fees "unearned" by underperforming validators is automatically redistributed among the other validators based on their relative performance-weighted stakes.
4. **Commission Rate:**
    - **Initial Values:** The overall base commission rate (`C`) and the split factors (`E`, `P`) will initially be fixed percentages (C=26%, E=100%, P=0%).
    - **Future Configurability:** Future upgrades may explore making this base commission rate adjustable via governance.
5. **Vote Tracking:** The consensus layer (Heimdall) will need to track each validator's milestone voting activity accurately over the distribution period. This participation data must be reliably provided to the fee distribution smart contract.
6. **Implementation:** Requires new smart contracts for fee handling and distribution, incorporating logic for stake-weighting and participation scaling. Clients (Bor, Heimdall) need updates for fee redirection and vote tracking/reporting.

### Reward Formulas

**Symbol Definitions:**

- `T`: Total Fees collected in the period (Transaction Fees + MEV Fees)
- `C`: Block Producer commission rate (0.26, or 26%)
- `Sv`: Total stake (including delegation) held by validator `v`
- `S`: Total stake in the network (sum of all validators' stakes)
- `Pv`: Performance score of validator `v` (0 to 1, based on participation/correctness)
- `Np`: Total number of designated Block Producers active during a checkpoint.
- `Bp`: Number of blocks produced by a specific producer `p` during the checkpoint.
- `B_total`: Total number of blocks produced by *all* producers during the checkpoint.
- `E`: Factor for equal distribution portion (e.g., 0.6 for 60%).
- `P`: Factor for proportional distribution portion (e.g., 0.4 for 40%). (`E + P` should equal 1).

**Calculation Steps:**

1. **Total Commission Pool (`CommissionPool`):**`CommissionPool = T * C`
2. **Block Producer `p`'s Commission Reward (`Rp_commission`):**
    - Equal Share: `(CommissionPool * E) / Np`
    - Proportional Share: `(CommissionPool * P) * (Bp / B_total)`
    - Total Commission: `Rp_commission = [(CommissionPool * E) / Np] + [(CommissionPool * P) * (Bp / B_total)]`
3. **Pool for Validators (`Pool_validators`):**
`Pool_validators = T * (1 - C)`
1. **Performance-Weighted Stake Calculations:**
    - For each validator `v`: `PerformanceWeightedStake_v = Sv * Pv`
    - `TotalPerformanceWeightedStake = sum(PerformanceWeightedStake_v for all validators v)`
2. **Earned Reward for Validator `v` (`Rv`):** `Rv = (PerformanceWeightedStake_v / TotalPerformanceWeightedStake) * Pool_validators`
3. **Total Reward for Producer `p` (`Rp_total`):**`Rp_total = Rp_commission + Rv_bp` (where `Rv_bp` is the earned validator reward for producer `p` if they also act as a validator, calculated in step 5 using their `S_bp` and `P_bp`)

### **Example**

**Given Data:**

- Total Fees Collected (T): 1,200 POL
- Base Producer Commission Rate (C): 26% (0.26)
- Total Validator Stake (S_validators = S_v1 + S_v2 + S_v3): 6M POL
- Validator 1 Stake: 1M POL
- Validator 2 Stake: 2M POL
- Validator 3 Stake: 3M POL
- Validator 1 Performance (P_v1): 100% (1.0)
- Validator 2 Performance (P_v2): 90% (0.9)
- Validator 3 Performance (P_v3): 100% (1.0)
- Block Producer: Validator 1

**Calculations:**

1. **Total Commission Pool (`CommissionPool`):**`CommissionPool = T * C = 1200 * 0.26 = 312 POL`
2. **Block Producer (Validator 1)'s Commission Reward (`Rp_commission_v1`):**
Since `Np=1` and `Bp/B_total=1`:
`Rp_commission_v1 = [(312 * 0.6) / 1] + [(312 * 0.4) * 1] = 187.2 + 124.8 = 312 POL`
(As expected, Validator 1 gets the full commission pool as the sole producer).
3. **Pool for Validators (`Pool_validators`):**`Pool_validators = T * (1 - C) = 1200 * (1 - 0.26) = 1200 * 0.74 = 888 POL`
4. **Performance-Weighted Stake Calculations:**
    - Validator 1 (BP): `PWS_v1 = S_v1 * P_v1 = 1,000,000 * 1.0 = 1,000,000`
    - Validator 2: `PWS_v2 = S_v2 * P_v2 = 2,000,000 * 0.9 = 1,800,000`
    - Validator 3: `PWS_v3 = S_v3 * P_v3 = 3,000,000 * 1.0 = 3,000,000`
    - `TotalPerformanceWeightedStake = 1,000,000 + 1,800,000 + 3,000,000 = 5,800,000`
5. **Earned Validator Reward (`Rv`) Calculations:**
    - Validator 1 (BP) (`Rv_v1`):
    `Rv_v1 = (PWS_v1 / TotalPerformanceWeightedStake) * Pool_validatorsRv_v1 = (1,000,000 / 5,800,000) * 888 = (10/58) * 888 = (5/29) * 888 = 153.103 POL`
    - Validator 2 (`Rv_v2`):
    `Rv_v2 = (PWS_v2 / TotalPerformanceWeightedStake) * Pool_validatorsRv_v2 = (1,800,000 / 5,800,000) * 888 = (18/58) * 888 = (9/29) * 888 = 275.586 POL`
    - Validator 3 (`Rv_v3`):
    `Rv_v3 = (PWS_v3 / TotalPerformanceWeightedStake) * Pool_validatorsRv_v3 = (3,000,000 / 5,800,000) * 888 = (30/58) * 888 = (15/29) * 888 = 465.103 POL`
6. **Total Reward for Validator 1 (Block Producer):**`Rp_total_v1 = Rp_commission_v1 + Rv_v1 = 240 + 165.517 = 405.517 POL`

**Distribution Table**

| Role                         | Stake (POL) | Performance | Reward (POL) | Calculation                                                                                      |
| ---------------------------- | ----------- | ----------- | ------------ | ------------------------------------------------------------------------------------------------ |
| Validator 1 (Block Producer) | 1M          | 100%        | 465.10       | `(1200*0.26) + [(1M*1.0 / 5.8M_PWS) * (1200*0.74)]` (Commission + Validator Perf-Weighted Share) |
| Validator 2                  | 2M          | 90%         | 275.59       | `[(2M*0.9 / 5.8M_PWS) * (1200*0.74)]` (Validator Perf-Weighted Share)                            |
| Validator 3                  | 3M          | 100%        | 459.31       | `[(3M*1.0 / 5.8M_PWS) * (1200*0.74)]` (Validator Perf-Weighted Share)                            |
| **Total**                    | **6M**      |             | **1200.00**  |                                                                                                  |

## Fee Distribution
All transaction fees earned by block producing validators will be automatically routed to a third-party multisig controlled by Polygon Labs and Regen Financial. Block producing validators will be able to choose how they manage MEV fees. 

The address for the transaction fee multisig is `0x7Ee41D8A25641000661B1EF5E6AE8A00400466B0`.

Funds from the wallet will be redistributed to network validators on an approximately monthly basis, which commences as soon as the changes are implemented.

Assuming the changes proposed here are implemented in the Rio Hardfork on October 8, 2025, payment timelines will occur as follows: 

| DATE       | FEE PERIOD (Dates)     | FEE PERIOD (Epochs) | APPROX. PAYMENT DATE |
| :--------- | :--------------------- | :------------------ | :------------------- |
| 2025 Oct 8 | N/A                    | N/A                 | N/A                  |
| 2025 Nov 8 | October 8 - November 8 | TBD                 | Nov 15 - Nov 22      |
| 2025 Dec 8 | Nov 9 - Dec 8          | TBD                 | Dec 15 - Dec 22      |
| 2026 Jan 8 | Dec 9 - Jan 8          | TBD                 | Jan 15 - Jan 22      |


## Rationale

This fee redistribution model is proposed because:

- **Maintains Validator Incentives:** It provides a stake-weighted revenue stream for validators.
- **Incentivizes Active Participation:** Scaling rewards by voting participation directly encourages validators to maintain high uptime and contribute reliably to consensus, enhancing network liveness and security.
- **Fairly Rewards Block Producers:** Compensates producers through a direct commission for block production, plus standard validator rewards if they also contribute stake and participate in validation.
- **Aligns with Security:** It links rewards to both stake (security deposit) and active contribution (voting).

## Backwards Compatibility

- This proposal is intended to be implemented concurrently with or immediately following the activation of the VEBloP architecture defined in [PIP-64](https://forum.polygon.technology/t/pip-64-validator-elected-block-producer/20918/1).
- It is not backwards compatible with the current fee distribution model where producers directly receive fees.
- Requires deployment of new fee handling smart contracts.
- Requires updates to Bor clients to redirect fees and heimdall to store and provide milestone votes. Validators will claim rewards from the new contract system.

## Security Considerations

- **Smart Contract Security:** Audit requirements remain crucial.
- **Commission Rate:** Considerations about the base rate C still apply.
- **Participation Gaming:** Mechanisms should ensure that "voting" accurately reflects meaningful participation, preventing validators from trivially voting without performing actual validation if stateless validation is used.
- **MEV Centralization and Mitigation:** While the VEBloP model enhances throughput, concentrating block production in a single entity, it also centralizes MEV (Maximal Extractable Value) extraction opportunities. There's a risk that a producer could exploit this position maliciously (e.g., through excessive front-running or censorship). Mitigation strategies include:
    - **Social Consensus & Off-Chain Coordination:** In the immediate term, if a block producer is widely perceived by the validator set to be acting maliciously or unfairly exploiting MEV, validators can coordinate to trigger a producer rotation, effectively electing a new producer via Heimdall.
    - **Future Automated Checks (Optional):** Longer-term, the protocol could potentially incorporate basic MEV monitoring or heuristics during block validation. If detectable malicious MEV patterns exceeding defined thresholds are identified, the system could automatically trigger a producer rotation as an enforced protocol rule. Designing robust and manipulation-resistant automated checks remains an area for future research and development.

## References

- [PIP-64](https://forum.polygon.technology/t/pip-64-validator-elected-block-producer/20918/1): Validator-Elected Block Producer for Enhanced Throughput

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
