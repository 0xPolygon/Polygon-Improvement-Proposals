| PIP | TBA |
|---|---|
| **Title** | Priority Fee Sharing for Delegators |
| **Description** | Extend PIP-65 priority fee distribution to include delegators |
| **Author** | Just Hopmans (@HopmansJust) |
| **Discussion** | https://forum.polygon.technology/t/pip-priority-fee-sharing-for-delegators/21793 |
| **Status** | Draft |
| **Type** | Contracts |
| **Date** | 2026-03-11 |

## Abstract

This proposal extends PIP-65's priority fee distribution model to include delegators. Priority fees are accumulated on Polygon PoS, bridged periodically to Ethereum via the native PoS bridge, and fed into the existing StakeManager and ValidatorShare reward system. The existing reward-per-share accumulator distributes fees to delegators through the same mechanism already used for checkpoint rewards. Commission rates apply automatically. Delegators and validators claim through the same interface they already use. No new accounting system is required.

## Motivation

Polygon's founding documentation states:

> "These rewards are primarily meant to jump-start the network, while the protocol in the long run is intended to sustain itself on the basis of transaction fees." — Polygon Staking Economics

If transaction fees are intended to sustain the protocol, then the participants who provide the capital securing those fees should share in them. Today, they do not.

Following the Rio hardfork (PIP-65), Polygon PoS introduced a new economic model for priority fee distribution. The model allocates collected priority fees to block producers (26%) and validators (74%). Delegators are excluded from this revenue stream entirely. PIP-65 originally specified an automated smart contract for fee distribution. This was replaced by a manual multisig process two days before mainnet launch due to technical limitations (see PIP-65 forum update, October 6, 2025).

This creates a structural misalignment between economic contribution and economic reward. Approximately 105 permissioned validators collectively provide roughly 0.34% of total staked capital. The remaining 99.66% — representing approximately 33,796 delegators — secures the network through delegated stake but receives no share of priority fees.

### Structural concerns

**1. Delegators fund the revenue they are excluded from.** Delegated capital directly increases a validator's fee allocation under PIP-65, which distributes 74% of fees to validators weighted by total stake (including delegation). The delegators providing that capital receive none of the resulting priority fees. Their only yield is emission-based checkpoint rewards — the same rewards Polygon's own documentation describes as temporary.

**2. The commission system does not function as intended.** Large validators offer 0% commission because it costs them nothing — their real income is priority fees, which exist outside the commission system entirely. Smaller validators need commission revenue to cover infrastructure costs (~$929/month per PIP-65 forum discussion), but cannot compete when delegators default to 0%. For the largest validators, ROI on infrastructure cost exceeds 8,000%. Commission was designed to let validators price their service. When the dominant revenue stream sits outside it, that mechanism breaks.

**3. The gap grows over time.** POL supply decreases through the EIP-1559 burn mechanism (~28.2M POL burned in February 2026 alone). Validators compound on both emission rewards and priority fees. Delegators compound on emission rewards only, which continue to decrease. Approximately 24.11M POL was allocated to validators in the 30 days preceding this proposal. As priority fees grow and emission shrinks, delegators have diminishing economic incentive to stake — directly threatening the network's long-term security budget. This is precisely the scenario Polygon's founding documentation warned against.

**4. The permissioned validator set prevents market correction.** Delegators dissatisfied with the fee distribution cannot start competing validators. In an open validator set, market dynamics would pressure validators to share fees. In a closed set of ~105 permissioned slots (PIP-39), the only correction mechanism is exit — which reduces network security rather than improving it.

**5. Protocol spending amplifies the imbalance.** PIP-82 redirects base fees — which would otherwise be burned — to subsidize gas for agent-to-agent transactions. This reduces the burn that benefits all POL holders, while the resulting transaction activity generates priority fees that flow exclusively to validators.

### Precedent

This is not a novel design. Major proof-of-stake networks enforce fee sharing at the protocol level:

**Cosmos Hub:** All transaction fees and inflation rewards are pooled. A single validator commission rate applies to the combined total. Enforced by the x/distribution module.

**Cardano:** Transaction fees and reserve rewards are pooled per epoch. The stake pool operator's margin applies to the total. Enforced by protocol.

Major staking providers operating on Polygon — including Coinbase, Figment, Everstake, and P2P — already operate under this model on Cosmos.

## Specification

This proposal does not modify PIP-65's fee allocation model. How priority fees are divided among block producers and validators remains unchanged.

### What changes

**1. Collector contract on Polygon PoS.** A contract on Polygon PoS receives priority fees (replacing the current multisig as destination) and bridges them periodically to Ethereum via the native PoS bridge.

**2. Distribution function in StakeManager on Ethereum.** A new function in StakeManager takes the bridged fees, splits them per validator weighted by stake (the same formula already used for checkpoint rewards), applies each validator's commission rate, and adds the delegator portion to the existing delegatorsReward pool — the same pool that checkpoint rewards already use.

**3. ValidatorShare distributes automatically.** The existing rewardPerShare accumulator in ValidatorShare picks up the added fees and distributes them to delegators proportionally. No changes to ValidatorShare are needed. Delegators and validators claim through the same interface they already use.

### How this builds on the existing system

The current ValidatorShare contracts already handle reward-per-share distribution for checkpoint rewards. The rewardPerShare accumulator tracks rewards per validator, settles on every delegator interaction (delegate, undelegate, claim, transfer), and handles all edge cases including stake changes, undelegation, and token transfers.

This proposal adds priority fees as a second funding source into the same system. The accounting does not change. The claim mechanism does not change. The delegation tracking does not change. The commission rate application does not change.

### What stays the same

- ValidatorShare contracts — zero changes
- rewardPerShare accumulator — zero changes
- Claim mechanism — zero changes
- Delegation tracking — zero changes
- Commission rate application — zero changes
- Transfer hooks (PIP-69) — zero changes
- PIP-65's allocation formula between block producers and validators
- Checkpoint reward distribution
- The delegation and staking workflow for delegators

### Commission rate impact

Validators set their commission rate once. This rate applies to both checkpoint rewards (as it does today) and priority fees (new). No additional configuration is required.

This changes what commission means. Today, 0% commission costs a validator nothing — their real income is priority fees outside the commission system. Validators compete on who can give away the most. That is a scale game only large validators can afford. Under this proposal, 0% commission means distributing the full fee allocation to delegators. Commission becomes a meaningful economic decision. Validators compete on value — uptime, service, transparency, and fair commission rates. Small validators can win that competition.

## Rationale

### Why bridge to Ethereum rather than distribute on Polygon PoS?

The data needed to distribute trustlessly — ValidatorShare balances, commission rates, total stake — only exists on Ethereum. It is not available on the PoS execution layer. This has been the case since launch and has not changed after four hardforks.

Distributing on PoS today would require off-chain calculation by a trusted party — which is exactly how the current multisig works. Bridging to Ethereum and feeding into the existing ValidatorShare system removes that trust requirement entirely.

In the future, once Polygon PoS connects to AggLayer, staking data could be proven on PoS via ZK proofs. Delegators could then choose: claim on Ethereum and restake directly, or claim on PoS with lower gas costs. But that is a future upgrade — not a reason to postpone this proposal.

### Why use the existing ValidatorShare system rather than a separate claim contract?

Based on community feedback during the Draft stage, the implementation path was simplified. The existing ValidatorShare contracts already handle reward-per-share accounting, delegation changes, commission rates, and proportional distribution. Adding priority fees as a second funding source into this system avoids deploying new accounting logic. It also preserves a single claim interface for delegators — the same one they already use for checkpoint rewards.

### Why mandatory rather than optional?

In a permissioned validator set, optional fee sharing cannot be corrected by market dynamics. Delegators cannot start competing validators. Making fee sharing optional in a closed system preserves the structural advantage of incumbents. Protocol-level enforcement is the standard approach in Cosmos Hub and Cardano — and the validators operating on Polygon already comply with mandatory fee sharing on those networks.

### Why extend the existing commission system?

Delegators already understand commission rates when selecting validators. Extending this rate to cover all revenue creates a single transparent metric. Today, validators compete on who can give away the most — a scale game that favors incumbents. This proposal shifts competition to value: uptime, service, and fair commission rates. Small validators can win that competition. This mirrors Cosmos Hub's design, where a single commission rate applies to both inflation and transaction fees.

### Why does this matter beyond delegators?

Fee sharing creates the conditions for a reinforcing cycle. Meaningful staking yield from network fees — not just emissions — attracts more stakers, increasing security, which builds confidence for developers and users, driving activity and fees. This aligns the interests of delegators, validators, users, and Polygon Labs.

This also aligns validator incentives with network security. Under the current system, a validator's fee income is independent of their own stake — they earn the same priority fees whether they stake 100K or 10M POL. Under this proposal, fee distribution is weighted by total stake, including self-stake. Validators with more skin in the game earn more. This mirrors Cosmos Hub's design and creates healthier economic alignment.

Without fee sharing, the opposite applies: declining staking incentives lead to fewer stakers, reduced security, and a weaker network.

## Backwards Compatibility

One existing contract is modified: StakeManager receives a new distribution function for priority fees. The modification follows the same logic already used for checkpoint reward distribution. ValidatorShare contracts are not modified. The only change to existing infrastructure beyond StakeManager is redirecting priority fees from the current multisig to the new collector contract on PoS. Checkpoint rewards continue to function as they do today. Validators' existing commission rates apply automatically.

## Security Considerations

**StakeManager modification.** The new distribution function follows the same logic already used for checkpoint rewards. The surface area for new bugs is limited because it reuses existing state variables and the same distribution formula. Standard smart contract auditing practices apply.

**Snapshot manipulation.** The existing rewardPerShare accumulator in ValidatorShare handles this. Rewards settle on every delegator interaction (delegate, undelegate, claim, transfer). There is no snapshot window to game. This is the same mechanism that has been protecting checkpoint reward distribution.

**Commission rate frontrunning.** A timelock on commission rate changes (one distribution period delay) prevents validators from raising rates immediately before a distribution.

**Bridge security.** The proposal uses the existing Polygon PoS native bridge, inheriting its security model. No additional trust assumptions are introduced. If a bridge transaction fails or is delayed, the distribution for that period is delayed — but not lost. Fees sit in the collector contract on PoS until the next successful bridge. No funds are at risk, only timing. Future implementations may use AggLayer infrastructure for trustless, higher-frequency distribution once Polygon PoS is connected.

## References

- [Polygon Staking Economics](https://polygon.technology/blog/polygon-staking-economics) — Rewards and staking incentives
- [PIP-65: Economic Model for VEBloP Architecture](https://forum.polygon.technology/t/pip-65-economic-model-for-veblop-architecture/20933)
- [PIP-69: Full ERC-20 Functionality for Validator Share Tokens](https://forum.polygon.technology/t/pip-69-full-erc-20-functionality-for-validator-share-tokens/21083)
- [PIP-82: Agentic Commerce Gas Program](https://forum.polygon.technology/t/pip-82-agentic-commerce-gas-program/21721)
- [PIP-39: Validator Admissions into PoS Network](https://forum.polygon.technology/t/pip-39-validator-admissions-into-pos-network/17988)
- [Cosmos SDK x/distribution module](https://docs.cosmos.network/v0.46/modules/distribution/)
- [Cardano monetary policy](https://docs.cardano.org/about-cardano/explore-more/monetary-policy/)

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
