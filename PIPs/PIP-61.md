---
PIP: 61
Title: Pectra EIPs
Author: Jerry Chen (@cffls), Harry Rook (@hrook1)
Description: Proposes compatible Pectra EIPs
Discussion: https://forum.polygon.technology/t/pip-61-pectra-eips/20783
Status: Final
Type: Core
Date: 2025-03-19
---

## Abstract

This proposal specifies activating Pectra features (introduced by Ethereum—L1) on the Polygon PoS network (L2). 

## Motivation

As bor is a fork of geth, the practice of merging upstream changes gives Polygon PoS the opportunity to enable new features defined in the L1.

This proposal seeks to upgrade the network with a subset of EIPs included in Ethereum.

## Specification

The Pectra PIP will enable the following upgrades:

* [EIP-2537: Precompile for BLS12-381 curve operations](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2537.md)
* [EIP-2935: Save historical block hashes in state](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2935.md)
* [EIP-7623: Increase calldata cost](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7623.md)
* [EIP-7702: Set EOA account code](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7702.md) [(PIP-51)](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-51.md)

The following EIPs have been included, however, they will not be active or supported within the working client implementation: 

* [EIP-7549: Move committee index outside Attestation](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7549.md)
* [EIP-7840: Add blob schedule to EL config files](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7840.md)
* [EIP-7691: Blob throughput increase](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7691.md)

Compared to Ethereum’s Pectra upgrade, the following EIP will not be included in Polygon PoS specs:

* [EIP-6110: Supply validator deposits on chain](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-6110.md)
* [EIP-7002: Execution layer triggerable exits](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7002.md)
* [EIP-7251: Increase the MAX_EFFECTIVE_BALANCE](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7251.md)
* [EIP-7685: General purpose execution layer requests](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7685.md)

The aforementioned EIPs have been specifically excluded as they handle processing requests from the consensus layer, which is incompatible with Polygon PoS. 

## Backward Compatibility

This PIP is not backward compatible with the current implementation of Bor and will, therefore, require a hard fork.

## Security Considerations

None.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).


