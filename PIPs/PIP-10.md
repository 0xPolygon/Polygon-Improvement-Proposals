---
PIP: 10
Title: Increase StateSync Confirmations
Description: Proposal to increase the statesync confirmations in bor
Author: Krishna Upadhyaya (@0xKrishna), Manav Darji (@manav2401), Pratik Patil (@pratikspatil024)
Discussion: https://forum.polygon.technology/t/proposal-increase-statesync-confirmations/11779
Status: Stagnant
Type: Core
Date: 2023-04-18
---

## Abstract 

At block #39599624, The Polygon PoS chain experienced a reorg with a depth of 157 blocks. It was observed that the cause of this was due to two forks running with more than 16 blocks of depth, which is greater than one sprint length. As a result of this, the value of to (which is used to fetch the state sync events and calculated based on sprint length) was different on both forks, causing a bad block error to occur.

Following the bad block error, reorgs were occurring at a greater frequency than normal and block propagation was affected as peers would reject blocks and fail to broadcast them further. 

This caused issues with the user experience and chain stability which we seek to address with this proposed solution.

## Rationale

At the start of each sprint, when Bor commits the state (which includes state sync transactions), it fetches state sync events from Heimdall using the two arguments below:


1. `fromID` - value which is a unique, incremental identity of the state, and;
2. `to` - value, which is a timestamp. 

Using these two arguments Bor is directed to fetch from Heimdall all the state sync events that occurred between `fromID` and `to`.

The calculation of `to` occurs in Bor and depends on the sprint length, to is the timestamp of the block (`n`) which is a ‘sprint length’ behind the ‘current block’:

- ```
  n = (current block number) - (sprint length)
  ```

Once the block number of `n` is calculated, Bor fetches the block from its local database.

The issue can occur during network partitions when two forks are running in parallel. During a partition, there is a high chance that the timestamp of block `n` will differ between parallel forks. As the forks would be running in isolation, the block formation would not happen simultaneously, causing unequal timestamps, and by extension, values for `to`. 

The Delhi Hardfork reduced the sprint length from 64 to 16 blocks. As a result of this, Bor now fetches state sync events from Heimdall more frequently as it occurs at the start of each new sprint. The result of this is that Bor nodes running different forks could receive a different number of state sync events (due to the calculation ‘to’) from Heimdall during shallower partitions that tend to occur more frequently.

### Example 1. 

Assuming that we have two different forks, “A” and “B”, with a different number of state sync transactions at the start of a particular sprint:

If there are 2 state syncs in fork A and 3 state syncs in fork B, when forks A and B merge back, let’s say fork B merges back to fork A, then B will import A’s state. 

When nodes on fork B import the state from fork A, Heimdall will attempt to fetch the state sync from Bor. At this point (as mentioned above) Bor will calculate the block as `n = (current block number) - (sprint length)`. 

When nodes from fork B fetch block `n` from the local database, the calculation will be based on the state of fork B and not the one from the incoming chain. This causes the value of `to` to differentiate between the two forks and Heimdall will return 3 state syncs instead of 2. This will result in a `BAD BLOCK` error (invalid merkle root). 

## Specification

This PIP proposes to Introduce a new genesis parameter called `stateSyncConfirmationMultiplyer`. This will be used in conjunction with the current calculation to increase the block range used to calculate the `to` timestamp while fetching the state-sync events from Heimdall. 

### Genesis File
We will introduce a new genesis parameter `stateSyncConfirmationMultiplyer` which  will be stored as a `map[string]unit64` and used to calculate the block interval for state-sync confirmations.
```
"stateSyncConfirmationMultiplyer": { 
	"0": 1, 
	"HF_BLOCK": 8 
},
```

### Bor Consensus Rules

Current Implementation:
```
// Currently sprintLength is 16
to = GetHeader(currentBlockNumber - sprintLength).Time
```

Proposed Change:
```
// fetch stateSyncConfirmationMultiplyer from genesis file
stateSyncConfirmationMultiplyer := FetchStateSyncMultiplyer(currentBlockNumber)

to = GetHeader(currentBlockNumber - (sprintLength*stateSyncConfirmationMultiplyer)).Time
```

Summary:<br>
- Current sprint length = 16
- ***State sync confirmation multiple = 8 (Proposed)***
- The block range to calculate the to value will increase from 16 to 128 (16*8)

## Security Considerations

The proposed state sync confirmation multiple would lead to state sync transactions being delayed by ((128-16) * 2.25) ~ 252 seconds as the interval would be increased from 16 to 128 blocks.

## Backward Compatibility

This PIP will not be backward compatible with the current implementation of Bor and will therefore require a ***Hard Fork***.

It should be considered that due to the use of sprint length within the `stateSyncConfirmationMultiplyer`, any future changes to the sprint length will require a revisit to the calculation logic.

## Appendix

* [PIP-7: Delhi Hardfork](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-07.md)

## Copyright 
All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).

