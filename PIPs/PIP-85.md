---
PIP: 85
Title: VEBloP PIP-65 Priority Fee Formula Adjustment
Author: David Silverman (@oneski), Sandeep Nailwal, Nicholas Truslow, Parvez Shaikh, Vasanti Rode
Description: Proposes Update to the Priority Fee Formula
Discussion: https://forum.polygon.technology/t/pip-85-veblop-pip-65-priority-fee-formula-adjustment/21829
Status: Draft
Type: Core
Date: 2026-03-25
---

## Abstract
Priority fees are up 1000% (10x) since the start of the PIP-65 system. While there are a tremendous amount of fees being passed to validators, this revenue is not presently distributed in a way that is equitably ensuring success for all ecosystem participants. Delegators are not seeing these fees passed on in any meaningful manner, and there is great variability in the reward distribution amongst the validator set. This PIP seeks to address these two issues. First, by adjusting the PIP-65 priority payout formula to include an equal weight factor for the validator distribution as well as a split for stakers.

The PIP-65 Priority fee changes proposed are as follows. First, taking 50% of the validator pool and distributing it to stakers via periodic merkle claimers deployed on Ethereum. Second, adjusting the remaining validator pool to be distributed 75% under an equal weighted performance adjusted fashion, and 25% under the existing stake weight formula.

## Motivation
Since the inception of PIP-65 we have seen a 10x increase in the amount of priority fees, with over 5.4MM POL distributed to validators during the February distribution. These fees are essential towards ensuring validator sustainability. Even with the decrease in hardware requirements VEBloP has brought, the current formula, combined with the current validator commission market rate creates an unsustainable situation for many smaller validators. Additionally, it has always been the goal of the Polygon PoS Staking system to see these priority fees (previously MEV fees under the pre-PIP-65 system) shared back with delegators as they ultimately enable the privileged position validators hold. 

With sufficient data on the current performance of the PIP-65 system we propose adjusting the formula to more equitably distribute rewards across the validation set, while also for the first time, enshrine delegator’s rights to priority fees.

In order to see these changes implemented rapidly upon adoption of this PIP we recommend the delegator fee share distribution be completed by periodic (initially monthly) merkle tree distributors on Ethereum Mainnet and call upon the maintainers of staking UIs and software (for example: staking.polygon.technology) to integrate claiming these rewards into their flows.

This PIP also stipulates that when the PIP-65 system is enshrined in smart contracts or other consensus bound mechanisms of the chain, the changes made in this PIP are included as well (unless superseded or canceled by a future PIP).

## Specification

No direct onchain changes. 

Change the formula that governs PIP-65 distributions from block 85245000 onwards as follows:

New Variable Sf: Stakers Fee Rate (e.g.: 0.5 for 50%)

New Variable Ef: Equality Factor (e.g.: 0.75 for 75%)

Pool_validators is redefined as: 
- Pool_valdiators =  T * (1 - C) * 1-Sf


New distribution pool Pool_stakers is defined as:
- Pool_stakers = T * (1 - C) * Sf

Formula for Rv() is redefined as:
- Rv() = ((PerformanceWeightedStake_v / TotalPerformanceWeightedStake) * Pool_validators * (1 - Ef)) + (Pool_validators * Ef / Number_of_Validators * Pv)

Sf should be set to 0.5
Ef should be set to 0.75

New Cleanup Process:
- All Priority fees leftover after this calculation shall be sent to the burn address.

Distributions to benefit stakers shall be deployed as merkle claimers, integrated into staking.polygon.technology with reference code for interacting with them and made fully open source for other integrators to leverage.


## Activation

- Activation block:
  * For Mainnet - 85245000

## Backward Compatibility
This change is backwards compatible as there is no broken functionality caused by these changes for any validator, staker or integrator. Stakers (and UIs designed for staking) should integrate the reference code for interacting with the merkle claimers in order to obtain their priority fees.

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
