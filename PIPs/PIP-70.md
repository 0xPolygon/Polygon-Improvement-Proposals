---
PIP: 70  
Title: Increase Minstake to 100k POL  
Author: Harry Rook, Kaitlin Beegle  
Description: Increase the minimum self-stake for new validators to 100k POL  
Discussion: https://forum.polygon.technology/t/pip-70-increase-minstake-to-100k-pol/21176
Status: Final 
Type: Contracts  
Date: 2025-07-29  
---

## Abstract

Polygon PoS requires each new validator to self-stake a minimum amount of POL before joining the active validator set. This minimum self-stake is enforced by the `StakeManager` contract via a `minDeposit` parameter on the `stakeFor` function.  

Currently, the threshold is municipal at 1 POL; we propose to raise the minimum self-stake for new validators to **100,000 POL tokens**. This higher threshold will apply only to validators onboarding in the future.  

The goal is to strengthen network security and integrity by ensuring new validators have a significant stake at risk, without disrupting current validators or delegators.

## Motivation

The existing self-stake threshold for Polygon PoS validators is inadequate to ensure optimal security and incentive alignment. With the minimum currently at just 1 POL, an aspiring validator can join the validator set with virtually no skin in the game. This low bar presents several problems:

- **Weak incentive alignment**: Validators with negligible self-stake have less financial incentive to behave correctly. A higher self-bond means new validators have more at risk, aligning their interests with network health and security.

- **Opportunistic behavior ("sniping")**: The low entry requirement enables opportunistic validators to quickly spin up nodes with minimal stake to exploit situations like validator slot openings. By substantially raising the requirement, we aim to discourage “slot sniping” and ensure only serious, long-term participants enter the validator set.

## Specification

We propose to update the `minDeposit` parameter in the Polygon PoS `StakeManager` contract to **100,000 POL** (`100k * 10^18` in base units).  

Changing the `minDeposit` does not require a contract code upgrade; it is a configurable parameter. For this purpose, the `StakeManager` contract exposes a governance function:

```solidity
updateMinAmounts uint256 _minDeposit, uint256 _minHeimdallFee
```

This change affects the requirements for initial self-staking of new validators as follows:

- **StakeManager `minDeposit`**: The `StakeManager` contract on Ethereum maintains a public variable `minDeposit` denominated in POL. The `stakeFor(address user, uint256 amount, ...)` function enforces that any new validator’s stake amount must be ≥ `minDeposit`.  

We will raise this parameter to **100,000 POL**. After the update, any call to `stakeFor` with an amount less than 100k POL will revert with the `"not enough deposit"` error.

## Backwards Compatibility

This proposal is fully backward-compatible with the current Polygon chain system in the sense that it does not introduce any breaking changes to consensus or contract interfaces.

## Security Considerations

Overall, increasing the `minStake` substantially risks pricing out potential validators should the USD valuation of POL rise dramatically.  

For this reason, we have opted for a conservative **100k POL** initial increase.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).

