---
PIP: 66
Title: Allow early block announcements
Author: Jerry Chen, Manav Darji
Description: Allow primary block producers to announce block early for better block propagation.
Discussion: https://forum.polygon.technology/t/pip-66-allow-early-block-announcements-on-pos/20976
Status: Final
Type: Core
Date: 2025-05-21
---

## Abstract
This proposal seeks to implement an optimisation to the consensus rules to allow the primary block producers in the network to announce their block early (as soon as it's built) for better block propagation and reducing the chance of reorgs.

## Motivation
Currently, all block producers follow a fixed block time enforced by the consensus rules (2s for primary block producers), during which transactions are executed and the next block is assembled. With current hardware validators are able to create a full block in roughly 500ms (average case scenario) but have to wait for full 2s 
before announcing the block to rest of the network, as shown below: 

The field `header.Time` for primary validators is `parent.Time + 2s`. The line below in `consensus.Seal` function uses the header time to wait.
```go
delay = time.Until(time.Unix(int64(header.Time), 0)) // Wait until we reach header time
```

This introduces unnecessary idle time in the network. Moreover, it leads to delayed block propagation affecting critical applications and also increases the chance of reorgs as a backup block is announced by the secondary validator if the block from primary isn't seen on time. Allowing early announcement 
(if the block is ready) before it's expected time improves the propagation by a great factor.

## Specification
This proposal requires changes in the bor consensus rules to allow primary validators to announce blocks as soon as it's ready instead of waiting. This proposal also introduces changes in header verification logic in consensus allowing the rest of the network to import and process early blocks and reject maliciously sent headers.

The proposal won't change the announcement timings for non-primary validators as malicious actors could cause 1 block reorgs if allowed the same.

The current workflow is as below:
```mermaid
sequenceDiagram
    participant Primary as Primary Proposer
    participant Network as Network

    Primary->>Primary: Start block production cycle
    Primary->>Primary: Execute transactions & create block (usually 100-500ms, up to 2s)
    Note over Primary: Block is ready but held until 2s elapsed
    Primary->>Primary: Wait until full 2-second block time is reached
    Primary->>Network: Broadcast block to network
```

The `miner` module triggers a new block production and sends the block to be signed to the consensus engine under certain conditions mentioned below:
1. The block is full (i.e. the transactions included in the block fully occupy the gas limit of that block).
2. The block building time (2s for primary) has been completed. During the block building loop (when it tries to execute as many transactions as possible), if the window is completed, it interrupts the execution and sends the partially built block to consensus for sealing and signing.
3. There are no more valid transactions to execute.

All the transactions which arrive after the block has been submitted to consensus will be picked up in the next block. 

Proposed workflow:
```mermaid
sequenceDiagram
    participant Primary as Primary Proposer
    participant Network as Network

    Primary->>Primary: Start block production cycle
    Primary->>Primary: Execute transactions & create block (usually 100-500ms, up to 2s)
    Primary->>Network: Immediately broadcast block to network
```

Note that the validators still get the same time to build the block and can utilise it fully before announcing the block. Transactions arriving after block has been signed will be continue to be included in next block.

### Timings of block propagation

### Consensus changes

1. Once the block is built, it's sent to `consensus.Seal` function for signing. This function waits until the header time is reached and is responsible for timing the release of the block.
```diff
+delay = time.Until(time.Unix(int64(header.Time), 0)) // Wait until we reach header time for non-primary validators
+if successionNumber == 0 {
+  // For primary producers, set the delay to `header.Time - block time` instead of `header.Time`
+  // for early block announcement instead of waiting for full block time.
+  delay = time.Until(time.Unix(int64(header.Time-c.config.CalculatePeriod(number)), 0))
+}
-delay = time.Until(time.Unix(int64(header.Time), 0)) // Wait until we reach header time
```

2. `verifyHeader` function is then responsible for block validation (including validating the header time and the time at which we received a block).
```diff
+if header.Time-c.config.CalculatePeriod(number) > uint64(time.Now().Unix()) {
+  return consensus.ErrFutureBlock
+}
-if header.Time > uint64(time.Now().Unix()) {
-  return consensus.ErrFutureBlock
-}
```

The above check leaves a slight window for non-primary validators to act maliciously so we also need to handle it later once we know the succession of the validator in `verifySeal`
```diff
+if succession != 0 {
+  if header.Time > uint64(time.Now().Unix()) {
+    return consensus.ErrFutureBlock
+  }
+}
```

## Backwards Compatibility
This change modifies the consensus rules and hence will require a hardfork. 

## Security Considerations
This change does not introduce new security risks. The adjustment while altering consensus rules stays fair for all validators and doesn't allow anyone to act maliciously affecting the network badly. 

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
