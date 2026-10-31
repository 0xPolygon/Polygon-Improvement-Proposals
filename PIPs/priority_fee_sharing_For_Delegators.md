| PIP | TBA |
|---|---|
| **Title** | Priority Fee Sharing for Delegators |
| **Description** | Extend PIP-65 priority fee distribution to include delegators |
| **Author** | Just Hopmans (@HopmansJust) |
| **Discussion** | https://forum.polygon.technology/t/pip-priority-fee-sharing-for-delegators/21793 |
| **Status** | Review |
| **Type** | Contracts |
| **Date** | 2026-03-11 |

## Table of Contents

- Definitions
- Abstract
- Motivation
- Precedent
- Specification
- Simulated Impact
- Rationale
- Backwards Compatibility
- Security Considerations
- Acknowledgments
- References

## Definitions

- **Priority fees:** The tip portion of transaction fees on Polygon PoS, paid above the base fee. Base fees are burned. Priority fees are distributed to block producers and validators under PIP-65.
- **Delegator:** A POL holder who stakes through a validator without running validator infrastructure.
- **Validator share token:** An ERC-20 token on Ethereum representing a delegator’s proportional share of a specific validator’s total delegated stake.
- **Collector contract:** A smart contract deployed on Polygon PoS that accumulates priority fees and bridges them to Ethereum.
- **Commission rate:** The percentage a validator charges on rewards distributed to their delegators.
- **StakeManager:** The core staking contract on Ethereum mainnet that records validator stakes, commission rates, and validator share token addresses.

## Abstract

This proposal extends PIP-65’s priority fee distribution to include delegators. A collector contract on Polygon PoS accumulates priority fees and bridges them to Ethereum, where a distribution function feeds them into the existing ValidatorShare reward pools. Delegators claim through the same interface they already use for checkpoint rewards. No new claim mechanism is needed.

## Motivation

**Polygon’s founding documentation states:**

> “These rewards are primarily meant to jump-start the network, while the protocol in the long run is intended to sustain itself based on transaction fees.” — Polygon Staking Economics.

If transaction fees are intended to sustain the protocol, then the participants who provide the capital securing those fees should share in them. Today, they do not.

Following the Rio hardfork (PIP-65), Polygon PoS introduced a new economic model for priority fee distribution. The model allocates collected priority fees to block producers (26%) and validators (74%). Delegators are excluded from this revenue stream entirely. PIP-65 originally specified an automated smart contract for fee distribution. This was replaced by a manual multisig process two days before mainnet launch due to technical limitations (see [PIP-65 forum update, October 6, 2025](https://forum.polygon.technology/t/pip-65-economic-model-for-veblop-architecture/20933/14)).

This creates a structural misalignment between economic contribution and economic reward. Approximately 105 permissioned validators collectively provide roughly 0.34% of total staked capital. The remaining 99.66% — representing approximately 30,757 delegators — secures the network through delegated stake but receives no share of priority fees.

1. **Delegators fund the revenue they are excluded from.** Delegated capital directly increases a validator’s fee allocation under PIP-65, which distributes 74% of fees to validators weighted by total stake (including delegation). The delegators providing that capital receive none of the resulting priority fees. Their only yield is emission-based checkpoint rewards — the same rewards Polygon’s own documentation describes as temporary.
1. **The commission system does not function as intended.** Large validators offer 0% commission because it costs them nothing — their real income is priority fees, which exist outside the commission system entirely. Smaller validators need commission revenue to cover infrastructure costs (~$929/month per [PIP-65 forum discussion](https://forum.polygon.technology/t/pip-65-economic-model-for-veblop-architecture/20933/5)), but cannot compete when delegators default to 0%. Commission was designed to let validators price their service. When the dominant revenue stream sits outside it, that mechanism breaks.
1. **The gap compounds exponentially for a small group.** The top 5 validators received 45% of the March 17 payout. Those fees can be restaked, increasing their stake, increasing their next fee allocation. This is compound interest — exponential growth for a small group, while 30,757 delegators compound on emission rewards only, which continue to decrease. As priority fees grow and emissions shrink, stake concentrates further and delegators have diminishing economic incentive to participate — directly threatening the network’s long-term security budget.
1. **The permissioned validator set prevents market correction.** Delegators dissatisfied with the fee distribution cannot start competing validators. In an open validator set, market dynamics would pressure validators to share fees. In a closed set of ~105 permissioned slots (PIP-39), the only correction mechanism is exit — which reduces network security rather than improving it.
1. **Protocol spending amplifies the imbalance.** PIP-82 redirects base fees — which would otherwise be burned — to subsidize gas for agent-to-agent transactions. This reduces the burn benefiting all POL holders, while the resulting transaction activity generates priority fees that flow exclusively to validators.
1. **The current distribution is unverifiable.** Priority fees are distributed via a manual multisig process with no published schedule, no documentation of payout periods, and no public methodology for stake snapshots or performance scores. The three payouts since Rio (January 22, February 28, March 17, 2026) arrived after irregular intervals (37 days, then 17 days) with no explanation of what period each covers. Validators cannot independently verify what they received or why — making it impossible to fairly redistribute fees to delegators even voluntarily. This proposal replaces the opaque manual process with a deterministic on-chain system where every input and output is publicly verifiable.

## Precedent

The same validators operating on Polygon already share fees with delegators on other networks.

- **Cosmos Hub:** All transaction fees and inflation rewards are pooled. A single validator commission rate applies to the combined total. Enforced by the [x/distribution module](https://docs.cosmos.network/v0.50/build/modules/distribution). Coinbase, Figment, Everstake, and P2P — all Polygon validators — already operate under this model on Cosmos.
- **Cardano:** Transaction fees and reserve rewards are pooled per epoch. The stake pool operator’s margin applies to the total. Enforced by protocol.

## Specification

*Disclaimer: This document proposes a mechanism for extending priority fee distribution to delegators. Specific parameters (bridge threshold, timelock duration, transfer cap) are subject to further analysis and community feedback. Implementation details of the bridge trigger mechanism and the ValidatorShare contract modification are subject to refinement during audit and development.*

This proposal does not modify PIP-65’s fee allocation model. How priority fees are divided among block producers and validators remains unchanged.

### Overview

One new contract is deployed: a **collector contract** on Polygon PoS that accumulates priority fees and bridges them to Ethereum. On Ethereum, a **distribution function** splits the bridged fees per validator according to PIP-65 stake weights and feeds each validator’s share into their existing ValidatorShare reward pool — the same pool that already handles checkpoint rewards.

### Step 1: Fee collection

Priority fees currently flow to the multisig address (`0x7Ee41D8A25641000661B1EF5E6AE8A00400466B0`) as block rewards on every Polygon PoS block. This proposal redirects that flow to the collector contract — a Bor client parameter change, not a consensus or smart contract change.

### Step 2: Bridging (Collector contract on Polygon PoS)

The collector holds accumulated POL and bridges it to Ethereum when triggered.

**Bridge trigger (permissionless, pseudocode):**

```
function initiateBridge() external {
    require(
        balance >= bridgeThreshold ||
        block.timestamp >= lastBridgeTimestamp + maxBridgePeriod,
        "Threshold not met"
    );
    uint256 amount = min(balance, transferCap);
    queuedBridge = {amount, block.timestamp + timelockDuration};
}

function executeBridge() external {
    require(block.timestamp >= queuedBridge.executeAfter, "Timelock active");
    bridge.depositFor(distributor, queuedBridge.amount);
    lastBridgeTimestamp = block.timestamp;
}
```

Anyone can call `initiateBridge()` on Polygon PoS at <$0.01 gas. After the timelock expires, anyone can call `executeBridge()`. If an anomalous transaction is queued, governance participants have the timelock window to intervene.

> **Implementation note:** The pseudocode above illustrates a fully permissionless design. The safety parameters — transfer cap, timelock, and threshold — are the specification. The exact bridge trigger mechanism (permissionless, keeper-based, or operator-triggered with governance constraints) is an implementation decision. Any approach that preserves these safety parameters is acceptable.

### Step 3: Distribution on Ethereum

When bridged POL arrives on Ethereum, a distribution function allocates it across validators using PIP-65 stake weights and feeds each allocation into the existing reward system.

**Symbol definitions:**

- `T`: Total bridged POL to distribute
- `stake_v`: Total stake (self-stake + delegated) of validator `v`, read from StakeManager
- `totalStake`: Sum of all validators’ stakes
- `selfStake_v`: Self-stake of validator `v`, read from StakeManager
- `commissionRate_v`: Commission rate of validator `v`, read from StakeManager
- `ValidatorShare_v`: The ValidatorShare contract for validator `v`, which manages the reward-per-share accumulator for delegators

**Distribution function (pseudocode):**

```
function distribute(uint256 totalAmount) external {
    for each active validator v:
        // PIP-65 allocation: proportional to performance-weighted stake
        allocation_v = totalAmount × (stake_v / totalStake)

        // Apply commission (read from StakeManager)
        commission_v = allocation_v × commissionRate_v
        selfStakeShare_v = allocation_v × (selfStake_v / stake_v)
        delegatorPool_v = allocation_v - commission_v - selfStakeShare_v

        // Credit validator (commission + self-stake share)
        // Credit delegators via existing ValidatorShare reward pool
        ValidatorShare_v.addPriorityFeeReward(delegatorPool_v, commission_v + selfStakeShare_v)
}
```

The `addPriorityFeeReward()` function feeds into the same reward-per-share accumulator that ValidatorShare already uses for checkpoint rewards. This means:

- **No new claim contract.** Delegators claim through the existing interface.
- **No new accounting.** The ValidatorShare accumulator already handles settlement on stake, unstake, and transfer (PIP-69).
- **No new edge cases.** All gaming vectors (rapid stake/unstake, short staking, token transfers) are already handled by the existing system.
- **No additional gas cost for delegators.** Claiming priority fees is bundled with claiming checkpoint rewards.
- **Frequency-independent.** The accumulator produces the same fair result whether `distribute()` is called every 30 minutes, every day, or every month. More frequent distribution reduces the gaming window but does not change the per-share calculation. There is no minimum or maximum frequency required for correctness.

### What changes in ValidatorShare

ValidatorShare contracts need one addition: an `addPriorityFeeReward()` function (or equivalent) that accepts POL from the distribution function and adds it to the existing `delegatorsReward` pool. The reward-per-share accumulator, commission logic, and claim mechanism remain unchanged. This is a minimal, auditable change to an existing, battle-tested contract.

### Gas costs

Bridge operations on Polygon PoS are negligible. Distribution on Ethereum is a single transaction per cycle for 105 validators. Delegators pay zero additional gas — priority fee claims are bundled with existing checkpoint reward claims through the same ValidatorShare interface.

### Suggested parameters

|Parameter                   |Suggested value      |Adjustable by            |
|----------------------------|---------------------|-------------------------|
|Bridge threshold            |100,000 POL          |Protocol Council (PIP-54)|
|Maximum bridge period       |7 days               |Protocol Council (PIP-54)|
|Transfer cap per bridge TX  |100,000 POL          |Protocol Council (PIP-54)|
|Timelock on bridge calls    |24 hours             |Protocol Council (PIP-54)|
|Commission rate change delay|1 distribution period|Protocol Council (PIP-54)|

These parameters are suggestions. Community and validator feedback is encouraged.

### What does not change

- PIP-65’s allocation formula between block producers and validators
- The StakeManager contract on Ethereum mainnet
- Checkpoint reward distribution
- The delegation and staking workflow for delegators
- The delegator claim interface

### Commission rate impact

Validators set a single commission rate. This rate applies to both checkpoint rewards (as it does today) and priority fees (new). No additional configuration is required.

This proposal does not reduce validator income from priority fees. It changes where priority fees flow — through the commission system instead of around it. A validator who sets their commission high enough retains the same priority fee income as today. The choice is theirs. What changes is that the choice becomes visible to delegators.

Under this proposal, 0% commission means distributing the full priority fee allocation to delegators. Commission becomes a meaningful economic decision. Validators compete on value — uptime, service, transparency, and fair commission rates. Small validators can win that competition.

## Simulated Impact

Using the March 17, 2026 payout (Nonce 13, 15,479,283 POL) and current staking data. Three scenarios: the current system, this PIP alone, and this PIP combined with the [Base Reward PIP](https://forum.polygon.technology/t/pre-pip-base-reward-for-priority-fee-distribution/21815). Priority fees only — checkpoint rewards are unaffected by this proposal and excluded from the simulation to isolate its effect.

> **Methodology note:** Figures show the maximum yield available to delegators before commission. Validators set their own commission rate, which determines how much they retain. The simulation does not assume a specific commission rate.

### What the simulation shows

Today, delegators receive zero priority fees. This PIP changes that — but it does not create a level playing field on its own.

Validators must cover infrastructure costs before they can share with delegators. Coinbase received 1,709,782 POL from the March 17 payout. Stakebaby received 13,900 POL. Both meet the same performance requirements and sign the same checkpoints. After covering costs, Coinbase can share 99.4% of their payout. Stakebaby can share at most 31.8%. The delegator is penalized for choosing a smaller validator.

Without correcting this, fee sharing amplifies the existing concentration. Delegators migrate to validators who share the most. Large validators share more. They attract more delegation. They earn more fees. The cycle accelerates. Small validators who cannot share without going below cost lose delegators — making the situation worse than today, not better.

The Base Reward PIP breaks this cycle. Every performing validator covers infrastructure through a base reward before the split. With costs covered, both Coinbase and Stakebaby can share their full allocation — and since fees are distributed proportionally to delegated stake, the yield per POL becomes equal.

A validator who receives more than they need can lower their commission and pass the difference to delegators. A validator who receives less than they need cannot share at all — no commission rate fixes a shortfall. The system should err on the side of giving validators enough, and let the market determine the rest.

### Delegator yield per 10,000 POL staked (before commission)

|Validator         |S1: Current|S2: This PIP|S3: Both PIPs|
|------------------|-----------|------------|-------------|
|Everstake         |0          |55.1 POL    |41.1 POL     |
|Coinbase          |0          |50.4 POL    |41.1 POL     |
|P2P.org           |0          |43.4 POL    |41.1 POL     |
|PathrockNetwork   |0          |34.2 POL    |41.1 POL     |
|EMC2 Node (median)|0          |53.2 POL    |41.1 POL     |
|Stakebaby         |0          |18.4 POL    |41.1 POL     |

With both PIPs, every delegator can earn up to 41.1 POL per 10K staked regardless of validator — the actual amount depends on the commission rate each validator sets. The yield gap disappears. Validators compete on service, not on size.

These two PIPs are designed as a complementary pair. This PIP addresses the vertical inequity (validators vs. delegators). The Base Reward PIP addresses the horizontal inequity (large vs. small validators). Together, they fix fee distribution without breaking the validator set.

Full simulation spreadsheet available upon request.

> **Simulation assumptions:** This simulation assumes all 105 validators meet PIP-4 performance benchmarks and receive the base reward. The March 17 payout data shows 17 validators receiving less than 1,000 POL. A cross-reference with the Polygon staking API confirms all 17 are HEALTHY with performance scores above 97% — the low payouts are not explained by poor performance. Under the Base Reward PIP, underperforming validators would not receive the base reward, returning that allocation to the proportional pool. This makes the simulation conservative: actual delegator yields would be slightly higher, and the base allocation percentage would be lower than the 6.4% modeled here.

## Rationale

### Why this is fully trustless

The distribution function reads all inputs on-chain. The reward distribution uses the existing ValidatorShare accumulator. No Merkle tree, no off-chain computation, no trusted operator.

### Why bridge to Ethereum rather than distribute on Polygon PoS?

Staking happens on Ethereum. Delegators claim checkpoint rewards on Ethereum. Distributing priority fees on Ethereum preserves this workflow and feeds directly into the existing ValidatorShare reward system — no cross-chain data bridging required. The alternative — distributing on Polygon PoS — would require each delegator to bridge individually to restake, shifting costs to thousands of delegators instead of one protocol-level bridge.

### Why threshold-based bridging?

The collector bridges when accumulated fees exceed a threshold or when a maximum time period elapses — whichever comes first. This is self-regulating: high activity means more frequent, smaller bridges; low activity triggers the time fallback. The system is correct at any frequency, but safer at higher frequency: smaller bridge amounts, shorter gaming windows, and more granular on-chain records.

### Why this also solves the transparency problem

The collector contract and distribution function replace the current opaque manual process with a fully on-chain, verifiable system. Every bridge transaction is timestamped on both chains. The distribution logic is deterministic — the same inputs always produce the same outputs.

### Why mandatory rather than optional?

In a permissioned validator set, optional fee sharing cannot be corrected by market dynamics. Delegators cannot start competing validators. Making fee sharing optional in a closed system preserves the structural advantage of incumbents. Protocol-level enforcement is the standard approach in Cosmos Hub and Cardano — and the validators operating on Polygon already comply with mandatory fee sharing on those networks.

### Why extend the existing commission system?

Delegators already understand commission rates when selecting validators. Extending this rate to cover all revenue creates a single transparent metric. This mirrors Cosmos Hub’s design, where a single commission rate applies to both inflation and transaction fees.

## Backwards Compatibility

One new contract is deployed on Polygon PoS (collector). On Ethereum, ValidatorShare contracts receive a minimal addition (`addPriorityFeeReward` or equivalent) and a distribution function is deployed. The only infrastructure change is redirecting priority fees from the current multisig to the collector contract. All existing staking operations, checkpoint rewards, and claim interfaces remain unchanged.

## Security Considerations

### Bridge security

This proposal inherits the native PoS bridge’s security model — the same bridge that priority fees already cross today under the manual multisig process. This PIP does not introduce new bridge risk; it formalizes an existing flow with additional safeguards (transfer cap, timelock, threshold-based bridging). All parameters are governance-adjustable by the Protocol Council.

### Distribution function correctness

The distribution function reads on-chain data and performs arithmetic to route fees into existing ValidatorShare pools. The new distribution function should undergo at least one independent security audit before deployment.

### Snapshot manipulation

The existing ValidatorShare reward-per-share accumulator prevents gaming around snapshot blocks. Rewards accrue continuously based on actual participation — there is no snapshot window to exploit. Additionally, Polygon’s existing withdrawal delay (~80 epochs) prevents rapid stake/unstake gaming — an attacker cannot deposit and withdraw within the same distribution cycle.

### Commission rate frontrunning

A timelock on commission rate changes (one distribution period delay) prevents validators from raising rates immediately before a distribution.

### If a bridge transaction fails

Fees remain in the collector contract on Polygon PoS and are included in the next successful bridge. No funds are lost — only timing is affected.

### Future migration path

Future implementations may use AggLayer infrastructure for trustless, higher-frequency distribution once Polygon PoS is connected. The governance-adjustable parameters allow smooth migration without contract redeployment.

## Acknowledgments

- **LEGEND Nodes** (@LegendNodes) — Operator of Stakebaby validator #118. Co-author of the companion [Base Reward PIP](https://forum.polygon.technology/t/pre-pip-base-reward-for-priority-fee-distribution/21815), whose mechanism is used in the S3 simulation scenario.

## References

- [Polygon Staking Economics — Rewards and staking incentives](https://docs.polygon.technology/pos/reference/rewards/)
- [PIP-65: Economic Model for VEBloP Architecture](https://forum.polygon.technology/t/pip-65-economic-model-for-veblop-architecture/20933)
- [PIP-65 Update #14 — Manual Multisig Implementation](https://forum.polygon.technology/t/pip-65-economic-model-for-veblop-architecture/20933/14)
- [PIP-69: Full ERC-20 Functionality for Validator Share Tokens](https://forum.polygon.technology/t/pip-69-full-erc-20-functionality-for-validator-share-tokens/21162)
- [Pre-PIP: Base Reward for Priority Fee Distribution](https://forum.polygon.technology/t/pre-pip-base-reward-for-priority-fee-distribution/21815)
- [Cosmos SDK x/distribution module](https://docs.cosmos.network/v0.50/build/modules/distribution)
- [Cardano monetary policy](https://docs.cardano.org/about-cardano/explore-more/monetary-policy)

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).

-----

*Edit March 21, 2026: Updated to Review status. Added simulated impact (three scenarios: current, PIP 1 only, PIP 1 + Base Reward PIP). Replaced monthly bridging with threshold-based permissionless bridging. Added transfer cap, timelock, and bridge security mitigations. Added verifiability rationale. See edit history for full diff.*
