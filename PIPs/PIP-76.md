---
PIP: 76
Title: Madhugiri Hardfork
Description: Proposes Madhugiri Hardfork
Author: Adam Dossa, Sandeep Sreenath
Discussion: https://forum.polygon.technology/t/pip-76-madhugiri-hardfork/21377
Status: Final
Type: Core
Date: 2025-11-04
---

## Abstract

This PIP specifies the changes included in the Polygon chain hard fork named Madhugiri.

## Specification

• Codename: Madhugiri

## Activation

- Activation block:
  * For Amoy - `28,899,616`
  * For Mainnet - `80084800`

## Included PIPs:

* [PIP-74: Canonical Inclusion of StateSync Transactions in Block Bodies](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-74.md)
* [PIP-75: Change consensus time to 1 second](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-75.md)

## Included EIPs:

* [EIP-7883: ModExp Gas Cost Increase](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7883.md) 
* [EIP-7823: Set upper bounds for MODEXP](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7823.md)
* [EIP-7825: Transaction Gas Limit Cap](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7825.md)

## Security Considerations

The above EIPs are part of the [Fusaka Hardfork](https://eips.ethereum.org/EIPS/eip-7607) on Ethereum, these are included now to help mitigate potential DoS attack vectors. Hence, these inclusions improve the security stance of the network.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).


