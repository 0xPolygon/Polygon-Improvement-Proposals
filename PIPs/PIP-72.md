---
PIP: 72
Title: Witness-Based Stateless Verification
Author: Jerry Chen (fchen@polygon.technology)
Description: Introduces witness-based stateless verification to enable lightweight nodes that validate blocks without maintaining full state
Discussion: https://forum.polygon.technology/t/pip-72-witness-based-stateless-verification/21257/3
Status: Peer Review
Type: Core
Date: 2025-08-22
---

## Abstract

This proposal introduces witness-based stateless verification for the Polygon network, enabling nodes to validate blocks without maintaining the complete blockchain state. By leveraging cryptographic witnesses containing only the necessary state data for block execution, stateless nodes can participate in network validation with significantly reduced storage requirements (approximately 90GB for historical bytecodes and associated trie nodes versus over 1TB for full state in mainnet). This architecture complements the Validator-Elected Block Producer (VEBloP) model outlined in [PIP-64](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-64.md), allowing validators to verify high-throughput block production without the computational and storage burden of maintaining full state.

## Motivation

Polygon chain's state has grown to over 1TB, forcing validators to use high-capacity NVMe SSDs (2TB+) with significant RAM, leading to substantial operational costs and sync times measured in days or weeks for new nodes. As outlined in PIP-64, achieving 10,000+ TPS requires separating block production from validation, but without stateless verification, validators would still need powerful hardware to keep up with unbounded state growth, limiting the benefits of VEBloP. These requirements create significant barriers for smaller validators through high initial hardware investment, ongoing operational costs, and technical complexity of maintaining synchronized state. Stateless verification addresses these challenges by reducing storage requirements to ~90GB while enabling broader network participation and maintaining security guarantees.

## Specification

### Overview

Stateless verification introduces a new node operation mode where blocks are validated using cryptographic witnesses rather than locally maintained state. This system comprises:

1. **Witness Generation**: Full nodes generate witnesses containing necessary state data for block execution
2. **Witness Distribution**: A dedicated P2P protocol (`wit`) distributes witnesses across the network
3. **Stateless Validation**: Nodes execute blocks using witnesses to verify correctness without full state
4. **Bytecode Management**: Historical contract bytecodes are stored separately for execution support

### Witness Structure

A witness contains all state information required to execute and validate a specific block:

```
Witness {
    context: BlockHeader      // The block this witness belongs to
    
    headers: List[BlockHeader] // Historical headers in reverse order
                              // (parent, grandparent, etc.)
                              // Used for BLOCKHASH operations
    
    state: Set[TrieNode]      // MPT trie nodes for accounts and storage
                              // Contains all state accessed during execution
}
```

#### Witness Components

1. **Context Header**: The block being validated, with state root and receipt root for verification
2. **Historical Headers**: Parent blocks required for `BLOCKHASH` opcode execution. Before [Pectra hardfork](https://eips.ethereum.org/EIPS/eip-7600), The EVM's `BLOCKHASH` opcode can access up to 256 previous block hashes. For backward compatibility, witnesses will include headers for any blocks that might be accessed during transaction execution. The parent header is always included to provide the pre-state root for validation.
3. **State Nodes**: Merkle Patricia Trie nodes for accessed accounts and storage slots

Note: Contract bytecodes are not included in witnesses as they are stored locally.

### Witness Protocol (`wit`)

The witness protocol (`wit`) is implemented as a sub-protocol within the devp2p framework, operating alongside the main Ethereum wire protocol (`eth`), similar to how the `snap` protocol facilitates snap sync. As a capability-based protocol, nodes advertise their support for `wit/1` during the initial devp2p handshake, allowing peers to negotiate witness exchange capabilities. This design enables witness data to flow through a dedicated message space without interfering with standard block and transaction propagation, while maintaining full backward compatibility with nodes that don't support stateless operation.

#### Protocol Messages

```
Message Types:
    NewWitness       (0x00) - Broadcast witness data to peers
    NewWitnessHashes (0x01) - Announce available witnesses
    GetWitness       (0x02) - Request witness by hash and page
    Witness          (0x03) - Witness response with requested data
```

##### NewWitness (0x00)
Purpose: Broadcast witness data (or witness pages) to connected peers immediately after block production  
Payload Structure:
```
{
    hash: Hash            // Block hash this witness belongs to
    number: uint64        // Block number
    page: uint64          // Current page number (0-indexed)
    totalPages: uint64    // Total number of pages for this witness
    data: bytes           // RLP-encoded witness data (full witness if totalPages=1,
                         // or partial witness chunk if paginated)
}
```
When Sent: Immediately after a block is sealed by the producer  
Recipients: All connected peers that support the witness protocol  
Pagination: If witness exceeds 16MB, it's automatically split and the first page will be broadcast
Notes: Peers must buffer and reassemble paginated witnesses; missing pages can be requested via GetWitness

##### NewWitnessHashes (0x01)
Purpose: Announce availability of witnesses without sending the full data  
Payload Structure:
```
{
    hashes: Array[Hash]    // Block hashes for which witnesses are available
    numbers: Array[uint64] // Corresponding block numbers for each hash
}
```
When Sent: Periodically broadcast to inform peers of available witnesses  
Use Case: Allows peers to discover witnesses they may have missed  

##### GetWitness (0x02)
Purpose: Request specific witnesses from peers  
Payload Structure:
```
{
    requestId: uint64               // Unique ID to match response
    witnessPages: Array[{
        hash: Hash                  // Block hash
        page: uint64                // Page number (0-indexed)
    }]
}
```
When Sent: When a node needs a witness for block validation  
Response Expected: WitnessMsg with matching requestId  

##### Witness (0x03)
Purpose: Response containing requested witness data  
Payload Structure:
```
{
    requestId: uint64               // Matches the request ID
    witnessPages: Array[{
        data: bytes                 // RLP-encoded witness chunk
        hash: Hash                  // Block hash
        page: uint64                // Current page number
        totalPages: uint64          // Total pages for this witness
    }]
}
```
When Sent: In response to GetWitness request  
Pagination: If `page >= totalPages`, indicates invalid request  
Data Format: RLP-encoded for efficient serialization

#### Protocol Flow and Pagination

The RLPx protocol imposes a 16MB message size limit. During high activity periods when many storage slots are accessed, RLP-encoded witnesses can exceed this limit, requiring automatic pagination. Nodes must buffer pages in order, reassemble the complete RLP-encoded witness, and then decode it for validation. Missing pages can be requested individually via GetWitness.

**Typical Message Sequence**:
1. Block producer generates witness during block creation
2. Producer broadcasts `NewWitness` messages (paginated if > 16MB)
3. Producer periodically sends `NewWitnessHashes` to announce availability
4. Nodes missing witnesses request via `GetWitness` with specific page numbers
5. Peers respond with `Witness` messages containing requested pages

**Pagination Process**: 
1. The complete witness structure is first RLP-encoded into a single byte array
2. If the encoded size exceeds 16MB (the RLPx protocol limit), the byte array is split into chunks
3. Each chunk becomes a page with metadata (page number, total pages)
4. Recipients must collect all pages and concatenate them before RLP-decoding the complete witness

### Stateless Node Operation

#### Initialization Phase

During initialization, stateless nodes first download historical contract bytecodes and associated trie nodes (approximately 90GB total: 30GB for bytecodes and 60GB for trie nodes). These are essential because contracts deployed before stateless sync cannot be retrieved from witnesses, and the trie nodes enable efficient determination of missing contracts during downtime. After this initial sync, the node performs a fast forward by querying the latest milestone from Heimdall and beginning sync from a recent finalized block, avoiding the need to process the entire blockchain history.

#### Synchronization Modes

##### Fast Forward Sync

When a node is significantly behind (default: 6400 blocks, the current span length), it triggers fast forward sync, skipping directly to a recently finalized block from Heimdall milestones. This enables rapid synchronization for new nodes and quick recovery from extended downtime by avoiding the processing of historical blocks.

##### Sequential Sync

When a node is relatively current (within 6400 blocks of chain tip), it uses sequential sync. The node downloads missing blocks along with their witnesses, validates each block sequentially using the witness data, and updates local block headers without maintaining state, continuing this process until reaching the chain tip.

### Witness Generation and Validation

#### Generation (Full Nodes)

Full nodes generate witnesses during block processing by first creating a witness with the parent header, then tracking all accessed state during execution (including account reads/writes, storage slot accesses, and contract code executions), finalizing the witness with all accessed state after execution completes, and broadcasting it via the witness protocol.

#### Validation (Stateless Nodes)

Stateless nodes validate blocks by first verifying that the witness pre-state matches the parent block's root (rejecting invalid witnesses), then re-executing all transactions using the state data from the witness, and finally confirming that both the computed post-state root and receipt root match the block header.

### Witness Storage and Management

#### Storage Format

Witnesses are stored in the database with the following structure:
- **Key**: `witness-<block-hash>` 
- **Value**: RLP-encoded witness data
- **Index**: Block number to hash mapping for efficient range queries

#### Pruning

Automatic pruning maintains manageable storage by retaining witnesses for the most recent 64,000 blocks (approximately 35 hours at 2-second block times) and running cleanup every 120 seconds to delete witnesses older than the retention threshold. A pruning cursor tracks the last pruned block to ensure consistent cleanup across restarts.

## Rationale

### Design Decisions

#### 1. Bytecodes Excluded from Witnesses

Contract bytecodes are deliberately excluded from witness payloads for several compelling reasons. Since bytecodes are immutable, once deployed, they do not change across blocks, making their repeated transmission wasteful. Including bytecodes in every witness would make the payloads unnecessarily large, potentially adding hundreds of megabytes to each witness and significantly increasing network overhead. Instead, the architecture employs a more efficient approach where nodes perform a one-time download of historical bytecodes and associated trie nodes (approximately 90GB total), which is far more bandwidth-efficient than repeatedly transmitting the same data with every witness.

#### 2. Witness Pruning

Automatic pruning is essential to maintain the practical benefits of stateless operation. Without pruning, witnesses would accumulate rapidly, potentially consuming gigabytes of storage per day during high transaction throughput periods. Most witnesses become irrelevant once blocks are finalized, as they are no longer needed for validation or reorganization purposes. If witnesses were stored indefinitely, the storage costs would eventually negate the primary benefits of stateless operation, undermining the goal of reduced hardware requirements and operational costs.

#### 3. Fast Forward Mechanism

The fast forward mechanism is crucial for enabling practical deployment of stateless nodes in production environments. By allowing nodes to skip historical block processing and jump directly to recent finalized blocks, the mechanism enables nodes to start validation almost immediately after initialization. This approach dramatically reduces initial synchronization time from weeks to hours, making stateless node deployment feasible for operators who need rapid network participation. Additionally, the fast forward mechanism prevents nodes from getting stuck processing extensive historical blocks during periods of network downtime, ensuring they can quickly catch up and resume normal operation.

### Trade-offs

#### Advantages

Stateless verification offers significant advantages over traditional full node operation. The most substantial benefit is dramatically reduced storage requirements, with nodes needing only approximately 90GB compared to over 1TB for full state (representing a greater than 90% reduction). This storage efficiency translates to much faster synchronization times, allowing nodes to sync in hours rather than the days or weeks required for full state sync. The reduced resource demands mean that standard consumer hardware becomes sufficient for node operation, eliminating the need for high-capacity NVMe SSDs and extensive RAM. These improvements collectively enhance network scalability by enabling more participants to run validation nodes without significant hardware investment.

#### Limitations

Despite its advantages, stateless verification comes with certain limitations that must be considered. Stateless nodes cannot maintain a transaction pool since they lack the complete state needed to validate pending transactions, limiting their ability to participate in transaction broadcasting and mempool management. The approach also requires higher network bandwidth due to the continuous downloading and propagation of witnesses, which can consume more bandwidth than the incremental state updates used by traditional full nodes. The system also introduces trust assumptions as it relies on the availability of witnesses from honest full nodes, creating a dependency on the network's honest majority for continued operation.

## Backwards Compatibility

### Protocol Level

The implementation preserves complete compatibility with existing infrastructure. All current P2P protocols remain unchanged, ensuring that existing node software continues to function without modification. The witness protocol operates as an additive enhancement rather than a replacement for existing protocols, allowing it to coexist with current network communication mechanisms. Full nodes continue operating normally and can participate in the network exactly as they did before, with no required changes to their core functionality.

### Node Operation

Node operators have complete flexibility in adopting stateless capabilities. Stateless mode is entirely opt-in through configuration settings, meaning existing full nodes can continue operating without any changes. Full nodes can choose to enable witness generation while maintaining their full state, allowing them to support stateless peers without compromising their own operation. The network supports a mixed environment where full nodes and stateless nodes operate simultaneously, with each type contributing to network health according to their capabilities.

### Validator Limitations

While stateless verification maintains protocol compatibility, validators operating in stateless mode experience certain functional limitations. Most significantly, stateless validators cannot manage transaction mempools since they lack the complete state data required to validate pending transactions. This means stateless validators cannot participate in transaction broadcasting, mempool maintenance, or fee estimation services that rely on comprehensive state access. Validators requiring mempool functionality must continue operating as full nodes to maintain these capabilities.

### Consensus

The consensus mechanism remains completely unchanged by this proposal. Block validation rules continue to apply identically for all node types, ensuring that stateless nodes reach the exact same validation results as full nodes when processing the same blocks. The proposal introduces no changes to finality mechanisms or fork choice rules, maintaining the network's existing consensus guarantees and security properties.

## Security Considerations

### Witness Integrity

Witnesses provide strong cryptographic guarantees for data integrity through multiple verification mechanisms. The pre-state root contained within each witness must precisely match the parent block's state root, ensuring that the starting state is authentic and has not been tampered with. After block execution, the computed post-state root must exactly match the state root specified in the block header, providing cryptographic proof that the execution was performed correctly. Any witness that fails these verification checks is automatically rejected by the node, preventing the acceptance of invalid or malicious state data.

### Data Availability

The system employs multiple complementary mechanisms to ensure robust witness availability across the network. Multiple independent full nodes generate identical witnesses for each block, creating natural redundancy that eliminates single points of failure. The dedicated P2P protocol ensures redundant distribution of witness data across many network participants, making witnesses highly available even if some nodes become unavailable. Additionally, witnesses can be regenerated on-demand from any full node that maintains complete state, providing a recovery mechanism when witnesses are temporarily unavailable or when new nodes join the network.

### Attack Vectors

The stateless verification system faces several potential attack vectors, each with corresponding mitigation strategies.

#### Witness Withholding

In this attack scenario, malicious nodes deliberately refuse to share witnesses with requesting peers, potentially preventing stateless nodes from validating blocks. The system mitigates this threat through multiple witness sources, ensuring that honest nodes can still obtain necessary witnesses from alternative peers. Additionally, penalty mechanisms can be implemented to discourage witness withholding behavior and maintain network health.

#### Invalid Witness Distribution

Malicious actors might attempt to distribute witnesses containing incorrect state data, hoping to cause stateless nodes to accept invalid blocks or reach incorrect validation results. This attack is effectively countered through cryptographic verification against block headers, where any witness containing invalid state will fail the mandatory pre-state and post-state root checks, automatically rejecting the malicious data.


## References

- [PIP-64: Validator-Elected Block Producer](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-64.md)

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
