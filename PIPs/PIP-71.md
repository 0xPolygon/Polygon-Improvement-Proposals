---
PIP: 71
Title: Increase gas limit to 60M
Author: Manav Darji, Sandeep Sreenath
Description: Proposes increasing block gas limit from 45M to 60M in the Polygon PoS network.
Discussion: 
status: Draft
Type: Core
Date: 2025-13-08
---

## Abstract
Increase maximum gas from 45M to 60M to provide greater capacity, allowing for more transactions in a single block.

## Motivation
As the network continues to increase its usage, the demand for gas will increase, potentially creating a bottleneck for transaction volumes.

By increasing the gas limit by 33%, headroom is created for upcoming work related to validator elected block 
producer (proposed in [PIP-64](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-64.md)) to 
accomodate growing demand.

## Specification

Validators can implement this change at the client level without requiring consensus changes. This client-based change will allow the network to uniformly implement higher gas so that individual validators do not cause bottlenecks during a particular span. 

**Parameter Changes**:
- Current Block Gas Limit: 45,000,000 gas
- Proposed Block Gas Limit: 60,000,000 gas

To ensure uniformity, the newly proposed gas limit will be part of the execution layer client binary itself (instead of validators trying to explicitly set it via flag).

The default value in [miner config](https://github.com/0xPolygon/bor/blob/v2.2.9/miner/miner.go#L61-L62) will be updated.

```diff
- GasCeil:  45_000_000,
+ GasCeil:  60_000_000,
```

## Backwards Compatibility
This change is non-breaking and backward-compatible.

## Security Considerations
The increased block gas limit may result in larger blocks requiring more time to propagate across the network. Testing will ensure that this change does not significantly increase uncle rates or network desynchronization.

Validators must be capable of handling the updated gas and ensure it does not exceed their CPU, memory, and disk resources.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
