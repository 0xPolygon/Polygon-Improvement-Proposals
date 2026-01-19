---
PIP: 77  
Title: Deterministic Finality via Lite Milestones  
Description: Re-implementing milestones using CometBFT vote extensions to achieve faster deterministic finality in Polygon PoS  
Author: Angel Valkov (@avalkov), Jerry Chen (fchen@polygon.technology)  
Status: Final  
Type: Core  
Date: 2025-03-01  
---

## Abstract

This proposal outlines a new approach to milestone finalization that significantly reduces finality time by re-implementing the existing [milestones](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-11.md) mechanism. The design builds on CometBFT vote extensions, introduced with the `heimdallv2` release, to enable deterministic and low-latency milestone finality.

## Motivation

The current milestone implementation has several limitations:

1. A milestone can only be proposed after at least 16 Bor block confirmations, which results in slow finalization.
2. Milestone proposal relies on a single designated proposer. If this proposer’s Bor node is lagging, on a divergent fork, or otherwise unhealthy, milestone finalization can be delayed while a new proposer is selected.
3. Milestones are proposed via Cosmos SDK transactions. This introduces additional latency, as inclusion depends on transaction propagation and the next Heimdall block proposer. Even with optimizations to the above points, this approach does not provide guarantees on the Heimdall height at which a milestone will be proposed or finalized.

Together, these factors make milestone finality slower and less predictable than desired.

## Specification

With `heimdallv2`, the consensus layer migrates from the legacy Tendermint implementation to CometBFT, which introduces support for vote extensions. Vote extensions allow validators to attach non-deterministic data to their consensus votes. Data attached at height `H` becomes available for processing at height `H+1`.

This mechanism enables each validator to propose a sequence of Bor block hashes starting from the last finalized milestone. At height `H+1`, the protocol evaluates all received vote extensions and identifies the longest common prefix of Bor block hashes that is supported by at least two-thirds of the total voting power. The final block in this agreed sequence is then finalized as the next milestone.

The proposed data structure for milestone propositions is as follows:

```go
type MilestoneProposition struct {
	BlockHashes      [][]byte
	StartBlockNumber uint64
	ParentHash       []byte
	BlockTds         []uint64
}
```

To limit overhead and avoid slowing vote propagation, the number of block hashes included in a single proposition is capped by MaxMilestonePropositionLength. This cap is sufficient given that milestones can be finalized every Heimdall block (with an approximate block time of 1.2 seconds), and larger payloads provide diminishing returns while increasing network cost.

### Fast-Forward Mechanism

In certain scenarios—such as when the consensus layer experiences downtime while the execution layer continues producing Bor blocks—waiting for milestones to advance sequentially would be inefficient. To address this, a fast-forward mechanism is introduced.

If the gap between the latest finalized milestone and the current Bor tip exceeds a configurable threshold, the system switches to fast-forward mode. In this mode, milestone proposals skip ahead by a fixed block interval, allowing milestones to catch up more quickly.

The logic is illustrated below:

```go
if isFastForwardMilestone(latestHeader.Number.Uint64(), milestone.EndBlock, params.FfMilestoneThreshold) {
    propStartBlock = getFastForwardMilestoneStartBlock(
        milestone.EndBlock,
        params.FfMilestoneBlockInterval,
    )
}

func isFastForwardMilestone(
	latestHeaderNumber,
	latestMilestoneEndBlock,
	ffMilestoneThreshold uint64,
) bool {
	return latestHeaderNumber > latestMilestoneEndBlock &&
		latestHeaderNumber-latestMilestoneEndBlock > ffMilestoneThreshold
}

func getFastForwardMilestoneStartBlock(
	latestMilestoneEndBlock,
	ffMilestoneBlockInterval uint64,
) uint64 {
	return latestMilestoneEndBlock + ffMilestoneBlockInterval
}
```

This approach ensures that milestone finality remains efficient and responsive even after extended periods of consensus-layer disruption, without compromising safety or determinism.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).