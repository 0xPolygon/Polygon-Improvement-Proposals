---
PIP: 3
Title: Auction Mechanism - A Mechanism to replace bad and under-performing validator
Description: Proposes a mechanism to replace under-performing validators
Author: Delroy Bosco
Discussion: https://forum.polygon.technology/t/pip-3-auction-mechansim-a-mechanism-to-replace-bad-and-under-performing-validators/8270/6
Status: Stagnant
Type: Contracts
Date: 2021-06-01
---

## Abstract

The Polygon chain has a soft limit of 100 validators securing the network. Once 100 validators have been onboarded to secure the network, there should be a way to add newer and highly proficient validators to the network to replace under-performing and bad validators. This particular mechanism of adding new validators is called the Auction Mechanism.

## Motivation

A Polygon (Matic) started the Mainnet a year ago, we have always wanted to onboard highly proficient validators to secure the network. Over the period of 1 year, we have managed to onboard proficient and ethical validators to the network, however, there are a few under-performing validators that have been observed during this period. The Auction Mechanism is a way to replace under-performing and bad validators with proficient and technical ones. Polygon aims to have all validators with high uptime and high performing and engaging validators to secure the Polygon chain.

## Specification

The Auction functionality is currently disabled through Polygon's Smart Contracts. Through the Auction Mechanism, a Validator will go through a bidding Mechanism where the newer validator has to bid higher than the current validator that is occupying the slot. Once the Auction period is over, whoever has the highest bid at the end of it becomes the validator.

To understand this in more detail I will add some specifics.

A Dynasty is formed by adding a chunk of checkpoints together. 1 Dynasty on the Polygon chain equals 80 checkpoints. A Dynasty is only a matter of reference. It does not perform any special duties on the chain.

During a Dynasty, 20 checkpoints are allotted for Auctions. Only during these 20 checkpoints is where validators will be under auction. The auction period will last only for 20 checkpoints. Right now each checkpoint is submitted to Ethereum approximately every 30 mins.

Validators who are in the auction pool are selected as per this logic

```
require(
            (_currentEpoch.sub(validators[validatorId].activationEpoch) % dynasty.add(auctionPeriod)) < auctionPeriod,
            "Invalid auction period"
        );

```

Where for example, your validator activation checkpoint (epoch) is 1 then windows for bidding are `21-40, 101-120`, and so on. Technically speaking, as a validator, you wouldn't need to calculate this and keep this ready. The Staking UI will ensure that each validator would know when they are in an auction period or not.

Once a validator is in the auction pool, it will be open to bids. Any new/provisional validator can bid on any of the validators that are in the auction pool. To make a successful bid, they will have to bid higher than the "**Overall Stake**" of a validator and not just the "**Self Stake**" of the validator.

Once the validator successfully bids a higher bid than the overall stake of a current validator, the current validator will be notified on the Staking UI and they will have to ensure that, that if they want to keep their place as a validator they will need to bid higher than the current bid.

*Auction Pool = Validators that are currently under auction*

For example, If a validator's self stake is 100 and overall stake is 1000. A new validator bids 2000 for his slot in the auction. For the current validator to outbid him, they will have to raise their stake by 901, thus their overall stake would increase and then the current validator becomes the highest bidder.

*Note: The current validator only needs to increase the overall stake value during the bidding process and not necessarily compare it with the `self stake`.*

Depending on the Bid status at the end of the Auction period, whoever has the highest bid gets to keep the validator slot. If a new validator's bid was the highest at the end of the auction period, then that validator will replace the old validator with `Confirming Bid`. Only once the new validator Confirms the Bid, only then he will be part of the active `validator set`. Until then he will not be part of it.

If there were no bids on any validator during the auction period, then the validator resumes normal operations.

**Note: During the auction period, a validator that is currently under auction still assumes normal operations like creating blocks and signing checkpoints.**

This cycle continues for every dynasty where various validators will be under auction at a given point in time.

For more details on the Auction Code you can check it out here:

**Auction Smart Contract**: [https://github.com/maticnetwork/contracts/blob/v0.3.0-backport/contracts/staking/stakeManager/StakeManagerExtension.sol](https://github.com/maticnetwork/contracts/blob/v0.3.0-backport/contracts/staking/stakeManager/StakeManagerExtension.sol)

## Rationale

The rationale behind this proposal is very simple. We at Polygon strive to build the best products and infrastructure to ensure Dapps and users get the best experience. In order of that, we also need to ensure that the Polygon chain is secured by high-performing and efficient validators too.

If the chain is only secured by a few highly performing validators then it isn't secure enough. The Auction mechanism is a way to remediate this problem, where new and highly performing validators can replace underperforming and bad validators through a secure and just process.

## Backwards Compatibility

To enable Auction, it is merely a config change in the Smart Contracts deployed by Polygon on Ethereum. If this proposal is met with a consensus, then the owner of the Smart Contracts will only need to make a small transaction on Ethereum to enable Auctions. Similarly, if the auction mechanism is disrupting the network in any way, we can also go ahead and disable the auction through the contracts.

In the future, this would ideally be a governance vote for enabling or disabling auctions.

## Security Considerations

As far as we think, we don't see any security considerations involving enabling Auctions on the Chain. This would induce a lot of engagement and pro-activeness on the validator front to ensure that they are always updated about everything.

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
