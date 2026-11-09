---
PIP: 54   
Title: Reassign Upgradeability Rights of Polygon PoS Contracts to Protocol Council  
Author: Mateusz Rzeszowski, Devashish Tomar, Harry Rook, Christopher von Hessert
Description: Protocol Council granted ownership of PoS L1 contracts. 
Discussion: https://forum.polygon.technology/t/pip-54-reassign-upgradeability-rights-of-polygon-pos-contracts-to-protocol-council/20350 
Status: Final 
Type: Contracts 
Date: 2024-12-17 
---

## Abstract

This proposal seeks to replace all rights of the legacy multisigs, amongst those the ability to upgrade staking and bridge smart contracts of the Polygon PoS chain, with the Protocol Council multisig contracts introduced in [PIP-29](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-29.md), which consist of a set of 2 Safe multisig smart contracts:

* Protocol Council Standard route, requiring 7 of 13 consensus of the Protocol Council with a 10-day timelock.
* Protocol Council Emergency route, requiring 10 of 13 consensus of the Protocol Council without a timelock.

## Motivation

This proposal aims to improve the security and efficiency of Polygon PoS’ Ethereum mainnet contracts’ upgradeability and operational administrative rights by facilitating a more secure, transparent, and open governance process.

By transferring ownership to the Protocol Council, the community can ensure that:

* The security of the underlying contracts is improved:
  * By an increase to the number of signers necessary for malicious collusion for emergency changes, i.e., from 5 to 10, while maintaining approximately the same quorum quotient — 5/7to 10/13,
  * The addition of a highly restrictive emergency change route, facilitated by 10 of 13 Protocol Council consensus, allows for efficient critical bug fixes and;
  * A geographically and jurisdictionally diverse set of publicly accountable signers operate the Protocol Council emergency and regular upgrade routes.
* Two pathways for upgrades, i.e., emergency and regular, enhance efficiency and bolster security.
* The transition from legacy multisigs to the Protocol Council will enable more transparent community participation in regular changes by using the staked token holder signaling framework, as defined in [PIP-50](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-50.md).

## Definitions:

**Operational Responsibilities:**

Tasks or actions performed by a contract that is essential to its core functionality and network execution. These responsibilities typically involve managing real-time processes, such as executing token transfers, validating checkpoints, and processing deposits and withdrawals.

Example: Managing validator staking processes in the `StakeManagerProxy` contract or checkpoint root hash storage in the `RootChainProxy` contract.

**Administrative Responsibilities:**

Rights or privileges that enable changes to a contract's configuration, management, or control settings. These responsibilities often include ownership, access control (roles), and upgradeability, ensuring that the contract's behavior and permissions are modifiable.

Example: The ability to upgrade contracts through the Protocol Council multisig.

**Protocol Council:**

A governance body consisting of publicly accountable individuals that manage the upgradeability and administrative control of Polygon's critical smart contracts. It operates through two multisigs:

  -  Standard Route (7 of 13 quorum): For regular             upgrades with a 10-day timelock.
  -  Emergency Route (10 of 13 quorum): For immediate         critical changes without a timelock.

**Roles:**

Specific privileges are assigned to addresses within a contract to enable restricted actions. Examples include `DEFAULT_ADMIN_ROLE`, `MAPPER_ROLE`, and `MANAGER_ROLE`. These roles grant specific permissions, like updating mappings or managing tokens, and are essential to maintaining contract security and functionality.

**Timelock:**

A mechanism that delays the execution of proposed changes to allow for community review and the ability to exit the system if needed. A timelock is applied to the Standard Route changes managed by the Protocol Council.

## Specification

Below, we set out how the existing Polygon PoS L1 contracts are managed between the Protocol Council Standard and Emergency upgrade paths managed by two separate multisig contracts.

### Multisig details

|Name|Signature Policy|Timelock (days)|Address|Network|
| --- | --- | --- | --- | --- |
|Legacy|5 of 9|2|0xFa7D2a996aC6350f4b56C043112Da0366a59b74c|Ethereum|
|Legacy|5 of 9|0|0x355b8e02e7f5301e6fac9b7cac1d6d9c86c0343f|Polygon PoS|
|Protocol Council Standard|7 of 13|10|0x9A53B1651C8Dd587c30392f8931e61daBBB50899|Ethereum|
|Protocol Council Emergency|10 of 13|-|0x37D085ca4a24f6b29214204E8A8666f12cf19516|Ethereum|
|Protocol Council Standard|7 of 13|10|TBD|Polygon PoS|
|Protocol Council Emergency|10 of 13|-|TBD|Polygon PoS|

### Role-based Contract Segmentation

Due to their differing security profiles, administrative rights and operational responsibilities are clearly distinguished. Administrative rights, which involve upgradeability, are critical for ensuring the system's security, and they are managed by a 10 of 13 signature policy.

Operational responsibilities, such as real-time processes and executing essential functions like staking, bridging, or checkpoint validation, are managed by a 7 of 13 signature policy.

Below, we show how each contract of the Polygon PoS chain will be managed, explicitly referring to its operational and administrative functions.

### Contract Upgradeability Ownership [Ethereum]

The following smart contracts are currently owned by the Legacy Timelock, whose proposer and executor roles are assigned to the Legacy Multisig `(0xFa…b74c)`.

Protocol Council 10 of 13 Multisig will become the sole owner of the following proxy contracts:


| **Contact Name**             | **Address**                                | **Type and owner**                        | **Function**                                                                    |
| ---------------------------- | ------------------------------------------ | ----------------------------------------- | ------------------------------------------------------------------------------- |
| `RootChainProxy `              | `0x86E4Dc95c7FBdBf52e33D563BbDB00823894C287` | Proxy and Timelock contract \| Owner      | Stores Checkpoint root hash.                                                    |
| `StakeManagerProxy `           | `0x5e3Ef299fDDf15eAa0432E6e66473ace8c13D908` | Proxy and Timelock contract \| Owner      | Manages staking for validators.                                                 |
| `DepositManagerProxy `         | `0x401F6c983eA34274ec46f84D70b31C151321188b` | Proxy and Timelock contract \| Owner      | Manages bridging in of tokens at Plasma.                                        |
| `WithdrawManagerProxy `        | `0x2A88696e0fFA76bAA1338F2C74497cC013495922` | Proxy and Timelock contract \| Owner      | Manages bridging out of tokens at Plasma.                                       |
| `EventsHubProxy`               | `0x6dF5CB08d3f0193C768C8A01f42ac4424DC5086b` | Proxy and Timelock contract \| ProxyAdmin | Responsible for emitting all events related to PoS consensus.                   |
| `RootChainManagerProxy`        | `0xA0c68C638235ee32657e8f720a23ceC1bFc77C77` | Proxy and Timelock contract \| ProxyAdmin | Manages deposits and withdrawals.                                               |
| `ERC20PredicateProxy  `        | `0x40ec5B33f54e0E8A33A975908C5BA1c14e5BbbDf` | Proxy and Timelock contract \| ProxyAdmin | Escrow contract for ERC20 tokens.                                               |
| `ERC721PredicateProxy`         | `0xE6F45376f64e1F568BD1404C155e5fFD2F80F7AD` | Proxy and Timelock contract \| ProxyAdmin | Escrow contract for ERC721 tokens.                                              |
| `ERC1155PredicateProxy`        | `0x0B9020d4E32990D67559b1317c7BF0C15D6EB88f` | Proxy and Timelock contract \| ProxyAdmin | Escrow contract for ERC1155 tokens.                                             |
| `MintableERC20PredicateProxy ` | `0x9923263fA127b3d1484cFD649df8f1831c2A74e4` | Proxy and Timelock contract \| ProxyAdmin | Escrow contract for mintable ERC20 tokens.                                      |
| `MintableERC721PredicateProxy` | `0x932532aA4c0174b8453839A6E44eE09Cc615F2b7` | Proxy and Timelock contract \| ProxyAdmin | Escrow contract for mintable ERC721 tokens, including Unstoppable Domains NFTs. |
| `MintableERC1155Predicate `    | `0x2d641867411650cd05dB93B59964536b1ED5b1B7` | Proxy and Timelock contract \| ProxyAdmin | Escrow contract for mintable ERC1155 tokens.                                    |
| `EtherPredicateProxy`          | `0x8484Ef722627bf18ca5Ae6BcF031c23E6e922B30` | Proxy and Timelock contract \| ProxyAdmin | Escrow contract for ETH.                                                        |
| `ChainExitERC1155Predicate`    | `0xDB2382413bCb9c2F1B6b62B52238558266361D68` | Proxy and Timelock contract \| ProxyAdmin | Responsible for emitting all events related to PoS consensus.                   |


Protocol Council 10 of 13 Multisig multisig will also assume control over the administration of the following functionalities:

| **Function Name**     | **Description** |
|---|---|
| `RootChain `        |`setNextHeaderBlock` - Update `_nextHeaderBlock_` <br> `setHeimdallId` - Update `_HeimdallID_` |
| `DepositManager`    | `updateRootChain` - Update `_rootchain_` contract |
| `WithdrawManager`   | `updateExitPeriod` - Update `_exit period_`  |
| `StakeManager`      | `migrateValidatorsData` <br> `insertSigners` |

### Contract Upgradeability Ownership [Polygon PoS]

The following smart contracts are currently owned by the Legacy Multisig on the Polygon PoS Network.

The Protocol Council 10 of 13 Multisig will become the sole owner of the following proxy contracts:

| **Contact Name**| **Address** | **Type and owner** | **Function**|
|---|---|---|---|
| `ChildChainManagerProxy`   | `0xA6FA4fB5f76172d178d61B04b0ecd319C5d1C0aa`    | Proxy \| ProxyAdmin          | Responsible for managing child contracts on PoS Chain. |
| `EIP1559Burn`              | `0x7A8ed27F4C30512326878652d20fC85727401854`    | Proxy \| ProxyAdmin          | Related to EIP1559Burn.                             |

### Contract Roles [Ethereum]

In addition to the upgradeability rights, some of these contracts, which have Roles library enabled and ownerships, do have different assigned roles to the Legacy Multisig.

These roles will be transferred to the Protocol Council 7 of 13 Multisig with a 10-day timelock.

|Contact Name|Roles|Address|
| --- | --- | --- |
|`RootChainManagerProxy`|`MAPPER_ROLE` & `DEFAULT_ADMIN_ROLE`|`0xA0c68C638235ee32657e8f720a23ceC1bFc77C77`|
|`ERC20PredicateProxy`|`DEFAULT_ADMIN_ROLE`|`0x40ec5B33f54e0E8A33A975908C5BA1c14e5BbbDf`|
|`ERC721PredicateProxy`|`DEFAULT_ADMIN_ROLE`|`0xE6F45376f64e1F568BD1404C155e5fFD2F80F7AD`|
|`ERC1155PredicateProxy`|`DEFAULT_ADMIN_ROLE`|`0x0B9020d4E32990D67559b1317c7BF0C15D6EB88f`|
|`MintableERC20PredicateProxy`|`MANAGER_ROLE` & `DEFAULT_ADMIN_ROLE`|`0x9923263fA127b3d1484cFD649df8f1831c2A74e4`|
|`MintableERC20Predicate`|`DEFAULT_ADMIN_ROLE`|`0x0f92D459B20D21F6bf9E02056EA9165d3f78bA62`|
|`MintableERC721PredicateProxy`|`DEFAULT_ADMIN_ROLE`|`0x932532aA4c0174b8453839A6E44eE09Cc615F2b7`|
|`MintableERC1155PredicateProxy`|`DEFAULT_ADMIN_ROLE` & `MANAGER_ROLE`|`0x2d641867411650cd05db93b59964536b1ed5b1b7`|
|`EtherPredicateProxy`|`DEFAULT_ADMIN_ROLE`|`0x8484Ef722627bf18ca5Ae6BcF031c23E6e922B30`|
|`StateSender`|`Ownership`|`0x28e4F3a7f651294B9564800b2D01f35189A5bFbE`|
|`GovernanceProxy`|`Ownership`|`0x6e7a5820baD6cebA8Ef5ea69c0C92EbbDAc9CE48`|

### Contract Roles [Polygon PoS]

In addition to the upgradeability rights, some of these contracts, which have Roles library enabled, do have different assigned roles to the Legacy Multisig

These roles will be transferred to the Protocol Council 7 of 13 Multisig with a 10-day timelock.

|Contact Name|Roles|Address|
| --- | --- | --- |
|`ChildChainManagerProxy`|`DEFAULT_ADMIN_ROLE` & `MAPPER_ROLE`|`0xA6FA4fB5f76172d178d61B04b0ecd319C5d1C0aa`|
|`ChildChain`|`Ownership`|`0xD9c7C4ED4B66858301D0cb28Cc88bf655Fe34861`|
|`RootSetter`|`Ownership`|`0xEb1CD9e44aB6BfE5a55EE96c468086e51B1B873a`|

## Backward Compatibility

This proposal is backward compatible in so far as the upgradeability of specified contracts is transferred to the Protocol Council.

## Security Considerations

The dual-pathway and contract segmentation based on function ensures non-overlapping execution paths for routine and critical actions, preserving operational integrity and security.

When comparing the Protocol Council and Legacy multisigs, quorum quotients are consistent with the legacy multi-sig while enhancing key redundancy and security.

The Standard Multisig has a 7 of 13 signature policy, representing 54% consensus, which closely mirrors the legacy 5 of 9 multisig (56%). This consistency ensures operational efficiency while expanding the signer set to 13.

An example of this can be seen with the upgradeability rights of the GovernanceProxy being transferred to the 7 of 13 multisig. This will allow the 7 of 13 Multisig to manage all operational tasks currently overseen by the GovernanceProxy contract.

In contrast, the Emergency Multisig increases the quorum to 10 out of 13 signers (77%). This increases resistance to collusion for critical actions, ensuring that high-impact decisions require broader agreement among signers. The proposal balances governance agility with enhanced security and key redundancy by maintaining a familiar quorum quotient for routine actions and introducing stricter requirements for emergencies.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).

