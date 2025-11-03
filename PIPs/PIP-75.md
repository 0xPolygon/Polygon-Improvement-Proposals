---
PIP: 75
Title: Change consensus time to 1 second
Description: Enable customizable block times and set consensus time to 1 second
Author: Jerry Chen (fchen@polygon.technology)
Discussion: 
Status: Draft
Type: Core
Date: 2025-11-03
---

## Abstract

This PIP bundles two related changes relating to block times on Polygon chain: enable customizable block time in the block producer, and change the consensus Period from two seconds to one second.

The feature introduces an operator override for producer cadence with a safety floor at the `consensus Period` setting the network Period to 1s. No block header fields or EVM semantics are changed. 


## Motivation

The current block production mechanism on Polygon faces two key limitations. First, block times cannot be configured with sub-second precision, preventing intervals like 1.5 seconds. Second, any adjustment to the block time necessitates a consensus-level hardfork, a cumbersome process for node operators.

This proposal addresses these limitations by building on the timing flexibility introduced in the Rio upgrade. The solution is two-fold: it exposes a strictly-bounded timing override that allows for sub-second granularity and standardizes the consensus `Period` to one second.

These changes will allow block producers to fine-tune block intervals, reducing latency and improving user experience. This is achieved without requiring a hardfork for future block time adjustments and without altering block header or timestamp validation rules.


### Producer timing feature

To enable configurable block times, this proposal introduces an optional "custom block time" setting for Bor’s block producer. This allows node operators to adjust the interval between block announcements with sub-second precision. When this setting is used, the producer calculates the next block’s announcement time based on the parent block’s actual announcement time. A crucial safeguard is that the client will still reject any custom interval shorter than the consensus `Period`, ensuring alignment with the existing consensus. If no custom time is set, the producer defaults to the consensus `Period`.

From an implementation perspective, a customizable `blockTime` duration will be added to block producers. This setting determines the interval between block announcements to control the overall block frequency, while `header.Time` continues to be represented in Unix seconds.

### Consensus parameter change

The second part of this proposal is a consensus parameter change. At a specific activation height, the consensus `Period` will be set to 1 second. Consequently, any custom block time interval set by the block producer could be reduced down to 1 second. This is the only consensus-level change in this PIP; the block header layout and state transition logic remain unchanged.


## Backwards Compatibility

Legacy clients lacking the Rio-gated producer logic or assuming a 2s Period may diverge after the fork. Validators, sentries, and RPC nodes will therefore require a hardfork to implement this change. 

## Security Considerations

Shorter intervals compress propagation budget per block. Operators should ensure reliable NTP, healthy peer connectivity, and adequate IO to handle a higher block count per unit time.  

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
