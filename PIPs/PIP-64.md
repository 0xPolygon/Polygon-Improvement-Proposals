---
PIP: 64
Title: Validator-Elected Block Producer
Author: Jerry Chen (fchen@polygon.technology)
Description: Introduces Validator-Elected Block Producers (VEBloP) for Enhanced Throughput on the Polygon PoS network.
Discussion: https://forum.polygon.technology/t/pip-64-validator-elected-block-producer/20918
Status: Peer Review
Type: Core
Date: 2025-04-29
---

## Abstract

This proposal outlines a new block production architecture for the Polygon PoS chain, introducing a Validator-Elected Block Producer (VEBloP). By designating a single elected producer per span and implementing stateless block validation, this architecture aims to substantially increase network throughput (targeting 10,000 TPS and potentially higher), shorten block confirmation times, and eliminate reorgs, all while maintaining decentralized block verification.

## Motivation

The current Polygon PoS network has a theoretical throughput ceiling of around 714 transactions per second (TPS), limited by the 30M block gas limit and 2-second block time. Achieving significantly higher throughput, like 10,000 TPS or more, faces several architectural hurdles within the existing system:

1. **Block Propagation Latency:** With potentially over 100 validators producing blocks, rapid propagation across the network is essential for consensus. Attempting to decrease block times below 1 second to boost TPS significantly strains the network, leading to latency issues and mini-reorgs due to inconsistent block views.
2. **Execution Bottlenecks:** Executing a full 30M gas block requires considerable time (roughly 125ms on 16 cores and 64GB RAM). When combined with validation and propagation overhead, this makes sub-second block times impractical under the current model.
3. **Lack of Single Slot Finality:** While [PIP-11](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-11.md) introduced faster finality, the current system doesn't guarantee single-slot finality. Small block ranges can still be reorganized after propagation, negatively impacting user experience and system predictability, especially with faster block intervals.
4. **Economic Unsustainability:** Scaling throughput increases hardware demands. For smaller validators, the rising costs may outweigh their diminishing share of transaction fees, potentially reducing validator participation and harming decentralization.
5. **Consensus Overhead:** Coordinating ~20 producers per span introduces contention and increases the likelihood of conflicting blocks when aiming for higher transaction inclusion rates.

Simply adjusting parameters like block time or gas limits is insufficient to reach 10k–100k TPS within the current decentralized production model due to the amount of state Polygon chain has; it's both technically challenging and economically unsustainable. This proposal tackles these challenges by separating block production from validation. A small group of high-capacity producers can build blocks quickly, while the broader validator set focuses on efficient, stateless verification. This allows for significant performance scaling without compromising the core trust assumptions of the consensus mechanism.

## Specification

### Overview of Architectural Changes

The proposed design fundamentally alters block production through several key changes:

- **Validator-elected Block Producer:** A single validator is elected as the exclusive block producer for a given span.
- **Backup Producers:** Designated backup producers stand ready to take over if the primary producer experiences downtime or misbehaves.
- **Stateless Validation:** Validators use witness proofs for lightweight, stateless block verification when settling milestones, reducing their hardware burden.
- **Decoupling Production and Validation:** Separating these roles enhances scalability while preserving trust assumptions.

These modifications aim to simplify block propagation, strengthen finality guarantees, and enable significantly higher throughput.

### Validator-elected Block Producer (VEBloP)

To address propagation delays, eliminate contention among producers, and achieve deterministic finality, this proposal introduces the concept of a single, validator-elected block producer (VEBloP) per span. Assigning block creation to one entity ensures blocks are produced predictably and rapidly, drastically reducing the potential for network-wide reorgs and enhancing overall liveness and reliability.

Under this model, Heimdall is responsible for selecting the primary block producer for each span. Heimdall also designates backup producers who can step in if the primary producer goes offline, ensuring continuous block production. This contrasts with the current system where around 20 producers operate within a span, any of whom can produce a block, leading to potential ordering conflicts and no instant finality. The new approach provides instant finality on transaction ordering and inclusion, though not yet on the block state itself, which will be later finalized by milestones.


| Feature                                   | Current System                 | Proposed VEBloP System             |
| ----------------------------------------- | ------------------------------ | ---------------------------------- |
| Producer Set Size per Span                | ~20                            | 1                                  |
| Block Producer Determination              | Any node from the producer set | Single node determined by Heimdall |
| Instant Txn Ordering & Inclusion Finality | No                             | Yes                                |
| Instant Block State Finality              | No                             | No                                 |


### Block Producer Election

In VEBloP, Heimdall employs a validator voting mechanism to select span producers. Validators cast their votes by submitting a ranked list of preferred producer candidates.
A candidate's position is determined by a weighted score that considers both their rank in a validator's vote and the validator's stake. This score is calculated using the formula: `weight = (max_possible_producers - position) × validator_stake`. This method gives greater weight to higher-ranked candidates, ensuring that validator preferences are reflected in proportion to their stake.
After scoring, candidates are ranked by their total weighted score, with ties broken deterministically by their candidate ID. To qualify, a candidate must meet a security threshold, securing at least `⌊(max_possible_weighted_vote_at_position × 2/3)⌋ + 1` of the voting power. The maximum possible weighted vote for a given position `P` is `(max_possible_producers - P + 1) × total_stake`. The selection process halts if any candidate fails to meet this `2/3+1` threshold to maintain security guarantees.
Finally, Heimdall selects the next producer from the list of qualified candidates who have maintained an active status by regularly voting on milestones (see the [Active Block Producer](#Active-Block-Producer) section). The selection cycles through the list, proceeding from the current producer. Initially, the consensus rules permit a maximum of 3 candidate block producers.

### Active Block Producer

An **active block producer** is a validator that has demonstrated recent participation in the consensus mechanism by submitting milestone propositions that achieve majority consensus. This concept is central to VEBloP's liveness guarantees and ensures that only engaged validators are qualified for block production duties.

#### Definition and Criteria

A validator is considered an active block producer if it:

1. **Participated in the Most Recent Milestone Voting**: The validator submitted a vote extension containing a milestone proposition that was part of the 2/3+ majority consensus for the latest milestone
2. **Maintained Good Standing**: The validator is not in the "failed producer" list (validators who have failed to perform their block production duties)

The system maintains the active producer set through a continuous milestone consensus process where validators propose milestones through vote extensions during consensus rounds, and those milestone propositions that achieve `≥2/3` of total voting power support determine the new "latest active producer" set from the validators who supported the majority milestone, with each successful milestone consensus completely replacing the previous active set.

### Span Rotation on Producer Failure

VEBloP implements an automatic span rotation mechanism to maintain network liveness when block producers fail to perform their duties. This failover mechanism prevents the blockchain from stalling due to inactive or unresponsive producers.

#### Trigger Conditions

Span rotation is automatically triggered when the following conditions are met:

1. **Insufficient Milestone Proposition Support**: No milestone proposition reaches 1/3+ voting power support
2. **Time Threshold Exceeded**: More than `5` Heimdall blocks (approximately 5-6 seconds) have passed since the last successful milestone was processed.

This combination indicates that the current producer is likely inactive or the network is experiencing consensus issues that require intervention.

#### Rotation Process

When rotation is triggered, the current producer responsible for blocks starting from the block immediately after the last successful milestone is marked as failed due to their inability to produce blocks that could be finalized by milestones. A new span is created with its start block set to the block immediately after the last successful milestone. The end block is set to the end block of the immediate next span. This calculation prevents excessive rotation frequency, especially when failures occur near span boundaries. The new producer set excludes both the current failed producer and any producer that isn't in the active producer set. Upon successful rotation, the failed producer is added to the failed producer list, and a 10-heimdall-block grace period is established before the next rotation check to prevent cascading failures.

#### Example

**Scenario**
- Span length: `100`
- Current span: `[200-299]` 
- Future span: `[300-399]`
- Producer failure occurs at block `280`
- Last successful milestone ended at block `279`

**Rotation Process**

1. Failure Detection: The Heimdall consensus identifies the producer responsible for blocks `200-299` as failed
2. New Span Creation: A replacement span is created with:
   - Start block: `280` (the block immediately after the last successful milestone)
   - End block: `399` (inherits the future span's end block)
3. Producer Assignment: The new span `[280-399]` is assigned to a different producer from the active set (excluding the failed producer)

**Continued Failure Handling**
If the new producer also fails and no progress is made in 10 Heimdall blocks, another rotation would create span `[280-400]` with yet another producer, continuing until a functioning producer successfully advances the chain.

### Bor Block Validation Under VEBloP


In VEBloP, Bor retrieves all validator and block producer information directly from Heimdall, eliminating dependency on block headers from previous blocks. Key changes include:

- **Direct Heimdall Queries**: Block producer eligibility is determined by Heimdall's span data rather than deriving from the validator set  in previous block headers
- **Removed Sprint Validators**: The validator set byte array previously embedded in block headers at sprint boundaries is removed post-VEBloP
- **Removed producer delay**: The producer delay (4 seconds) between each sprint is removed post-VEBloP


#### Bor Block Validation Rules

When a new block arrives, Bor applies the following validation logic:

#### Case 1: Same Producer as Parent Block

This scenario typically occurs when a producer continues their assigned span.

1.  **Fast Path (within 4 seconds):** If a block arrives within 4 seconds of its parent, it is considered timely. Bor validates it against the current producer span on Heimdall. *The 4-second threshold accounts for a standard 2-second block time plus 2 seconds for fluctuation in network latency.*

2.  **Slow Path (after 4 seconds):** If the block is delayed, Bor initiates a "span rotation detection" process to check if a producer change has occurred. It polls Heimdall every 200ms for up to 8 seconds.
    - If a new span is found, the block is **rejected**. This means a rotation occurred, and the old producer is no longer valid.
    - If no new span is found, the block is **accepted**, confirming the original producer is still valid.

#### Case 2: Different Producer from Parent Block

This scenario indicates an expected producer rotation.

1.  **Span Verification:** Bor checks Heimdall for a new span that authorizes the new producer.
    - If the new span exists, the block is **accepted** as a valid rotation.
    - If the new span does not exist, the block is **rejected**.

2.  **Timeout Protection:** Bor waits a maximum of 4 seconds for the new span to appear. This prevents the node from getting stuck if it receives a block from a malicious producer who is not in the validator set.

### Timing Parameters

| Parameter               | Value            | Purpose                                                         |
| ----------------------- | ---------------- | --------------------------------------------------------------- |
| Base timeout            | 4 seconds        | Base timeout for block propagation                              |
| Span check interval     | 200 milliseconds | Polling frequency when waiting for span updates                 |
| Same-producer wait      | 8 seconds        | Maximum wait when expecting possible rotation (2x base timeout) |
| Different-producer wait | 4 seconds        | Maximum wait to verify producer change (1x base timeout)        |


### Forced Transactions

To guarantee censorship resistance, even with a single block producer who could potentially misbehave, a mechanism for forcing the inclusion of critical transactions is necessary. This ensures a deterministic pathway for both user and system-level transactions to be executed, upholding protocol guarantees.

This proposal leverages Polygon chain's existing state sync mechanism in Bor. Users can submit transaction calldata and a target address to a designated L1 contract. This contract then triggers a state sync message directed to Bor. At the end of each sprint (currently every 16 blocks), the active block producer queries Heimdall for any pending state syncs. The producer must then include a transaction that calls a special L2 contract, which interprets the state sync data and executes the user's original transaction. Validators verify the correct inclusion and execution of these forced transactions as part of their standard consensus process. If a block producer fails to include a required forced transaction, validators will reject the block as invalid, which in turn triggers Heimdall to initiate a producer rotation.

### Stateless Verification

To make block validation efficient and scalable, particularly as throughput increases, this proposal incorporates stateless verification. This eliminates the need for validators to maintain and constantly update the full chain state, significantly lowering their hardware requirements and encouraging broader participation.

Stateless verification relies on cryptographic witnesses generated by the block producer (or any full node) during block construction, based on established techniques (e.g., [Execution layer cross-validation](https://gist.github.com/karalabe/47c906f0ab4fdc5b8b791b74f084e5f9)). These witnesses contain the necessary state information for a block to be verified without access to a full state database. Witnesses are computed and propagated across the network, potentially via a dedicated peer-to-peer protocol like the [Ethereum Witness Protocol (wit)](https://github.com/ethereum/devp2p/blob/master/caps/wit.md). Validators receive a block and its corresponding witness, allowing them to re-execute the block's transactions and confirm its validity statelessly. Validators only need to store recent verified blocks necessary for settling milestones and checkpoints. Strategies like witness caching (for both code and state) are being explored to minimize the data propagated per block.

### Economic Incentives

Due to its size and complexity, the detailed economic model is considered out of scope for this architectural specification and will be addressed in a separate proposal. Crucially, this proposal does not alter the existing staking rewards validators receive for participation in checkpoint proposing and signing on Heimdall and L1.

## Rationale

This design strikes a balance between performance and decentralization. Centralizing block production allows for lower latency and higher throughput by removing the bottleneck of coordinating many producers for each block. Simultaneously, maintaining decentralized verification through stateless validation ensures that trust assumptions are upheld, as a broad set of validators confirms the chain's integrity. This separation of concerns enables significant scaling while preserving the core security properties of the network.

## Backwards Compatibility

- Both Heimdall and Bor are required to implement the new time-based span structure, producer election/rotation logic, and stateless witness verification. A hardfork will be required when the new architecture is rolled out.
- Existing validators can choose to run stateless Bor clients, reducing their hardware needs without needing to operate full nodes.
- The processes for [checkpoints](https://docs.polygon.technology/pos/architecture/heimdall/checkpoints/) and milestones remain unchanged.

## Security Considerations

- **Network Trust:** The model assumes the block producer connects to at least some honest RPC nodes to prevent issues like block withholding.
- **Witness Integrity:** The security of stateless validation hinges on the integrity of witnesses. Validators must be able to reliably detect and reject invalid blocks based on incorrect or manipulated witnesses. Robust witness generation and propagation are critical.

## References

- [PIP-11: Milestone](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-11.md)
- [Ethereum Witness Protocol (wit)](https://github.com/ethereum/devp2p/blob/master/caps/wit.md)
- [Execution layer cross-validation (Gist by karalabe)](https://gist.github.com/karalabe/47c906f0ab4fdc5b8b791b74f084e5f9)
- [Polygon PoS State Sync](https://docs.polygon.technology/pos/architecture/bor/state-sync/)
- [Polygon PoS Checkpoints](https://docs.polygon.technology/pos/architecture/heimdall/checkpoints/)