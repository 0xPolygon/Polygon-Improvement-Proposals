---
PIP: 80
Title: P256 Precompile Gas Cost Adjustment
Author: Adam Dossa, Krishang Shah
Description: Increase gas cost of P256 precompile from 3450 to 6900
Discussion:
Status: Draft
Type: Core
Date: 2026-02-04
---

## Abstract

This PIP proposes increasing the gas cost of the P256 precompile contract, introduced in [PIP-27](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-27.md), from 3450 to 6900 gas, aligning with EIP-7951 specifications.

## Motivation

Ethereum's Fusaka hardfork includes EIP-7951, which revises the gas cost for the P256 precompile to 6900. This change ensures Polygon PoS maintains compatibility with Ethereum's gas pricing for the secp256r1 signature verification precompile.

## Specification

| Parameter | Current | Proposed |
|-----------|---------|----------|
| Gas cost  | 3450    | 6900     |

The precompile address (`0x0000000000000000000000000000000000000100`) and functionality remain unchanged. Only the gas cost is modified.

## Backward Compatibility

This is a consensus-breaking change that increases gas costs. Contracts relying on the P256 precompile will consume more gas after activation. This change requires a hardfork.

## Security Considerations

The gas increase aligns the precompile cost with its actual computational overhead as determined by EIP-7951.

## References

- [PIP-27: Precompiled for secp256r1 Curve Support](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-27.md)
- [EIP-7951](https://eips.ethereum.org/EIPS/eip-7951)

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
