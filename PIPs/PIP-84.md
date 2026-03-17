---
PIP: 84
Title: Giugliano Hardfork
Author: Lucca Martins (@lucca30)
Description: Proposes Giugliano Hardfork
Discussion: https://forum.polygon.technology/t/pip-83-giugliano-hardfork
Status: Draft
Type: Core
Date: 2026-03-10
---

## Abstract
This PIP specifies the changes included in the Polygon PoS hard fork named Giugliano.

## Specification

* Codename: Giugliano

## Activation

- Activation block:
  * For Amoy - 35573500
  * For Mainnet - TBD

## Included PIPs
* [PIP-66: Allow Early Block Announcements](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-66.md)
* [PIP-83: Embed EIP-1559 Gas Parameters in Block Header Extra Field](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-83.md)

## Background

PIP-66 was originally included in the [Bhilai hardfork (PIP-63)](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-63.md) but was subsequently reverted due to observed behavioral issues in the network. This hardfork reintroduces PIP-66 with a reviewed implementation that addresses all issues observed during the prior deployment.

PIP-83 introduces on-chain visibility for the configurable EIP-1559 gas parameters enabled by [PIP-79](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-79.md). It embeds the `GasTarget` and `BaseFeeChangeDenominator` values in the block header's extra data and exposes them via new and extended RPC methods, improving transparency and observability for network participants.

## Security Considerations
There are no additional security considerations beyond those described in the included PIPs.

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
