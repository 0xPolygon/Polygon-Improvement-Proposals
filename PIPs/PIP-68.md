---
PIP: 68
Title: Reform Key Polygon PoS Multisigs to Integrate Protocol Council Members
Authors: Harry Rook, Kaitlin Beegle
Description: Protocol Council members to replace existing signers and signature policies
Discussion: https://forum.polygon.technology/t/pip-68-reform-key-polygon-pos-multisigs-to-integrate-protocol-council-members/21008
Status: Final
Type: Contracts
Date: 2025-06-03
---
## Abstract

This proposal updates the signer composition and signature policy of the existing multisig wallets at:

* `0xFa7D2a996aC6350f4b56C043112Da0366a59b74c` (Ethereum)

* `0x355b8e02e7f5301e6fac9b7cac1d6d9c86c0343f` (Polygon PoS)

This change will establish control by the [Protocol Council]() with the existing multisig structure remaining unchanged, preserving its address, configuration, and established contract structure. Additionally, the signature policy will be modified to a 7-of-13 threshold.

## Motivation

This reform ensures operational continuity, transparency, and resilience by retaining familiar multisig infrastructure while adopting a more decentralized signer composition.

## Specification

The signer set and policy for the following multisigs will be updated:

| Name             | Signature Policy | Timelock (days) | Address                                     | Network     |
|------------------|------------------|------------------|---------------------------------------------|-------------|
| Protocol Council | 7 of 13          | 10               | 0xFa7D2a996aC6350f4b56C043112Da0366a59b74c  | Ethereum    |
| Protocol Council | 7 of 13          | 10               | 0x355b8e02e7f5301e6fac9b7cac1d6d9c86c0343f  | Polygon PoS |

#### Responsibilities 
The additional responsibilities being taken on by the Protocol Council for each chain are detailed below:

**Ethereum**

This multisig retains control over critical administrative functions for Polygon's Ethereum mainnet contracts. These responsibilities include executing key configuration updates and validator state management tasks across core protocol components:
* RootChain: `setNextHeaderBlock` to update the checkpointing block interval, and `setHeimdallId` to update Heimdall identifiers.
* DepositManager: `updateRootChain` to modify the reference to the RootChain contract.
* WithdrawManager: `updateExitPeriod` to change the exit duration for bridged assets.
* StakeManager: `migrateValidatorsData` to port validator data, and `insertSigners` to update consensus key signers.

**Polygon PoS**

This multisig controls upgradeability of contracts, `ChildChainManagerProxy` and `EIP1559Burn`, and inherits administrative ownership of roles across additional PoS-side components, including `ChildChain`, `RootSetter`, and other essential infrastructure contracts. Role transfers from the legacy multisig signers ensure that this route has full authority to manage both upgradability and privileged roles across the system.

## Backward Compatibility

The proposal is fully backward compatible with the existing security structure as it retains the existing multisig contract, changing only the signer composition and signature policy.

## Security Considerations

Using the existing multisig and updating the signer composition preserves established security practices, ensuring stability and minimal disruption during the transition. The updated signature policy of 7-of-13 maintains robust security and transparent governance.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).

