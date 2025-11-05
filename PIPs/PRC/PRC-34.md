| PIP | Title               | Description                                                                            | Author                                    | Discussion                                                                     | Status | Type            | Category | Created    |
| --- | ------------------- | -------------------------------------------------------------------------------------- | ----------------------------------------- | ------------------------------------------------------------------------------ | ------ | --------------- | -------- | ---------- |
| 34  | Polygon Staking Hub | Proposes the new staking layer architecture and standardizes smart contract interfaces | @Daniel Gretzke @Zero Ekkusu @Simon Dosch | [Forum](https://forum.polygon.technology/t/prc-34-polygon-staking-layer/13587) | Draft  | Standards Track | PRC      | 2024-02-23 |

# Abstract

This proposal introduces a standardized set of smart contracts, known as Polygon Staking Layer, to facilitate decentralized restaking within the Polygon ecosystem. The Staking Layer allows stakers to take on slashing risk and in return validate services.

The Polygon Staking Layer is comprised of:

### Staking Hub

The staking hub is the central contract of the staking layer, coordinating stakers, lockers and services. The main responsibility is acting as a registry for lockers and services, as well as ensuring the correct execution of slashings.

### Lockers

Assets staked within the Staking Layer are deposited into lockers. Lockers keep track of the deposited assets and their represented voting power, manage restaking risk, and execute slashings.

### Services

Stakers can restake (subscribe) towards different services, such as sequencer networks, prover networks or data availability committees. Restaking means that assets deposited into lockers can be repurposed multiple times across multiple services without fragmenting their liquidity.

### Slashers

Slashers are separate contracts managed by services. In case a staker performs a malicious action within a service, the service can utilize the slasher contract to prove the malicious action occurred which will notify the staking hub to perform the slashing.

# Motivation

The motivation behind the Staking Layer is to provide a central place for security within the Polygon ecosystem. This can include Chain Development Kit (CDK) networks, as well as other potential services within the Polygon ecosystem. The restaking mechanism allows for higher capital efficiency and lower liquidity fragmentation for stakers securing various aspects of the ecosystem. New services like new CDK chains benefit from a shared security model, which lowers the cost of securing the network or service.

# Specification

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).

## Staking Hub

The Staking Hub is the permissionless central contract that coordinates lockers and services. When services register themselves with the staking hub, they define:

- Lockers to be used with the locker: All assets that can be restaked to the service. Lockers need to be registered with the hub in order to be used by a service.
- Slashing percentages: The amount of stake that can be slashed from each allowed locker.
- Notice period: The amount of time required in order for a staker to unsubscribe from the service.
- Slasher contract: The contract that proves slash-able offenses and notifies the staking hub.

In order to restake their assets to a service a staker can subscribe to it using the Staking Hub. This allows the service to slash the staker in return, should they perform a provable malicious action. When a staker subscribes, they define a timestamp until when they choose to lock in their subscription with the service. It allows the staker to promise to stay subscribed until a certain point in time.

When a user no longer wishes to continue staking for a service, they can announce their unsubscription. The announcement allows a service to finalize any pending actions performed by the staker for the service. Additionally, it acts as a grace period where a staker is still freezable (slashable) and prevents a staker from performing a malicious action and immediately unsubscribing from a service. After the notice period expires, a staker can finalize their unsubscription.

Services can terminate a staker’s subscription at any time if the staker performs a malicious action. In such a case, a service can freeze a staker by proving the malicious action to the slasher contract. Once a staker has been frozen by a service, it can be slashed by the service. When other services freeze the staker as well, the freeze period is extended. During the freeze period, the staker can be slashed multiple times, to enable more granular slashing conditions, such as slashing based on the total amount of stake that performed a malicious action within a specific time period. While frozen, the staker cannot perform any actions on the staking hub.

In order to provide flexibility to a service as well as safety to the staker, the slasher contract should be a separate contract, but it can also be set to the service contract itself. A service contract might want to be behind an upgradeable proxy in order to be able to add more functionality over time. Therefore, it’s recommended to implement slashing conditions in a separate immutable contract to ensure that a compromised service is not able to slash stakers maliciously as well as have the guarantee that the slashing conditions do not change. A service can change the slashing contract. Changing the slashing contract initiates a delay, during which any staker that does not agree with the new slashing conditions can unsubscribe from the service.

### Communication

This section outlines all callbacks between different components on certain actions.

- Subscription
  - Staking Hub ⇒ Service: The service is notified when a staker subscribes alongside their lock in timestamp. This allows the service to perform checks, such as whether the staker is permitted to subscribe based on various parameters, such as:
    - Checking whether certain conditions within the service were met, e.g., the staker provided their BLS key beforehand.
    - Checking for staking thresholds: If a service supports multiple lockers, it can require a staker to have a minimum balance in one of the lockers, all of them, or calculate a dynamic voting weight.
    - Checking user inputs: A service might require a staker to be subscribed for a certain amount of time and might check the lock in timestamp.
  - Staking Hub ⇒ Lockers: Every locker utilized by a service is notified of the subscription. This allows the locker to perform various actions, such as:
    - Keeping track of a user’s stake within a specific service
    - Perform risk management and revert should a staker exceed a threshold in accumulated slashing percentages
- Unsubscription Initiation
  - Staking Hub ⇒ Service: Within the lock in period the service can prevent the staker from unsubscribing by reverting on the callback. After the period, the service can no longer prevent the staker from unsubscribing to prevent a malicious service from potentially holding the funds of a staker hostage. After the unsubscription initiation, the service should consider the staker as unsubscribed in order to prevent situations where a staker could perform a malicious action and immediately finalize the unsubscription. It is recommended for the defined notice period to be longer than the withdrawal delay within a locker, to ensure that funds can be frozen should a staker perform a malicious action in this transition period.
  - Staking Hub ⇒ Lockers: Lockers are notified when a user unsubscribes from a service in order to perform any necessary action. If this call reverts, the unsubscription initiation is also reverted, so services need to make sure to trust the lockers they want to include not to revert.
- Unsubscription
  - Staking Hub ⇒ Service: A service is notified that the staker has finalized their unsubscription. The service cannot revert the final unsubscription.
- Termination
  - Staking Hub ⇒ Lockers: A service can unsubscribe a staker at any time based on conditions chosen by the service without penalty. This unsubscription is immediate, and lockers are notified of the unsubscription.
- Freeze
  - Slasher ⇒ Staking Hub: When a staker performs a malicious action, the Staking Hub is notified by the slasher contract and freezes the staker. Subsequent freezes from other services extend the freeze period.
- Slash
  - Slasher ⇒ Staking Hub ⇒ Lockers: When a staker is frozen, a slasher can call the staking hub to perform stake slashes for multiple lockers. The accumulated slashes must not exceed the predefined maximum slashing percentages within one freeze period. The staking hub notifies all lockers, where funds should be slashed.

```solidity
// SPDX-License-Identifier: MIT OR Apache-2.0
pragma solidity 0.8.24;

struct LockerSettings {
    uint256 lockerId;
    uint8 maxSlashPercentage;
}

/// @title Staking Hub
/// @author Polygon Labs
/// @notice The Staking Hub is the central contract of the Polygon Staking Layer and is responsible for managing and coordinating stakers, lockers and services.
interface IStakingHub {
    /// @notice Emitted when a new locker is registered with the staking hub.
    /// @param locker The address of the locker.
    /// @param lockerId The assigned id of the locker.
    event LockerRegistered(address indexed locker, uint256 indexed lockerId);

    /// @notice Emitted when a new service is registered with the staking hub.
    /// @param service The address of the service.
    /// @param serviceId The assigned id of the service.
    /// @param lockers Lockers used by the service.
    /// @param slashingPercentages The compressed slashing percentages of the lockers.
    /// @param unsubNotice The delay period between unsubscription initiation and finalization.
    event ServiceRegistered(address indexed service, uint256 indexed serviceId, uint256[] lockers, uint256 slashingPercentages, uint40 unsubNotice);

    /// @notice Emitted when a staker subscribes to a service.
    /// @param staker The address of the staker.
    /// @param serviceId The id of the service.
    /// @param lockInUntil The time until the staker is locked in.
    event Subscribed(address indexed staker, uint256 indexed serviceId, uint40 lockInUntil);

    /// @notice Emitted when a staker initiates unsubscription from a service.
    /// @param staker The address of the staker.
    /// @param serviceId The id of the service.
    /// @param unsubscribableFrom The timestamp when the unsubscription can be finalized.
    event UnsubscriptionInitiated(address indexed staker, uint256 indexed serviceId, uint256 unsubscribableFrom);

    /// @notice Emitted when a callback to the service during unsubscription initiation reverts.
    /// @param staker The address of the staker.
    /// @param serviceId The id of the service.
    /// @param data The revert data.
    event UnsubscriptionInitializationWarning(address indexed staker, uint256 indexed serviceId, bytes data);

    /// @notice Emitted when a staker finalizes unsubscription from a service.
    /// @param staker The address of the staker.
    /// @param serviceId The id of the service.
    event Unsubscribed(address indexed staker, uint256 indexed serviceId);

    /// @notice Emitted when a callback to the service during unsubscription reverts.
    /// @param staker The address of the staker.
    /// @param serviceId The id of the service.
    /// @param data The revert data.
    event UnsubscriptionFinalizationWarning(address indexed staker, uint256 indexed serviceId, bytes data);

    /// @notice Emitted when a slasher update is initiated.
    /// @param serviceId The id of the service.
    /// @param newSlasher The address of the new slasher.
    /// @param scheduledTime The timestamp after which the slasher can be updated.
    event SlasherUpdateInitiated(uint256 indexed serviceId, address indexed newSlasher, uint40 scheduledTime);

    /// @notice Emitted when a slasher is updated.
    /// @param serviceId The id of the service.
    /// @param slasher The address of the new slasher.
    event SlasherUpdated(uint256 indexed serviceId, address indexed slasher);

    /// @notice Emitted when a staker is frozen.
    /// @param staker The address of the staker.
    /// @param serviceId The id of the service freezing the staker.
    /// @param until The timestamp until the staker is frozen.
    event StakerFrozen(address indexed staker, uint256 indexed serviceId, uint256 until);

    /// @notice Emitted when a staker is slashed.
    /// @param staker The address of the staker.
    /// @param serviceId The id of the service slashing the staker.
    /// @param lockerIds The ids of the lockers being slashed.
    /// @param percentages The percentages of each locker being slashed.
    event StakerSlashed(address indexed staker, uint256 indexed serviceId, uint256[] lockerIds, uint8[] percentages);

    /// @notice Registers a new locker with the staking hub.
    /// @dev Must be called by the locker contract.
    /// @dev Emits `LockerRegistered` on successful registration.
    /// @return id The new id of the locker.
    function registerLocker() external returns (uint256 id);

    /// @notice Registers a new service with the staking hub.
    /// @dev Must be called by the service contract.
    /// @dev Emits `ServiceRegistered` on successful registration.
    /// @dev Emits `SlasherUpdated`.
    /// @param lockers Settings for lockers. `lockers` must include 1-32 lockers. `lockerId`s must be ordered by locker id in ascending order. `maxSlashPercentage` cannot exceed `100`.
    /// @param unsubNotice Delay period between unsubscription initiation and finalization. Cannot be `0`.
    /// @param slasher The slashing contract used by the service. It may be the service contract itself.
    /// @return id The new id of the service.
    function registerService(LockerSettings[] calldata lockers, uint40 unsubNotice, address slasher) external returns (uint256 id);

    /// @notice Restakes staker to a service.
    /// @dev Cannot be called while the staker is frozen.
    /// @dev Calls `onSubscribe` on all lockers used by the service.
    /// @dev Calls `onSubscribe` on the subscribed service.
    /// @dev Emits `Subscribed` on successful subscription.
    /// @param service The service the staker subscribes to.
    /// @param lockInUntil The time until the staker is locked in and the service can prevent unsubscribing.
    function subscribe(uint256 service, uint40 lockInUntil) external;

    /// @notice Initiates unsubscription from a service.
    /// @notice Cannot be called while the staker is frozen.
    /// @dev Calls `onInitiateUnsubscribe` on the service. If the staker is locked in, the service can prevent unsubscription by reverting. If the staker is not locked in, the service cannot prevent unsubscription by reverting and forwarded gas is limited.
    /// @dev Emits `UnsubscriptionInitializationWarning` if the callback to the service reverts when the user is not locked in.
    /// @dev Emits `UnsubscriptionInitiated` on successful unsubscription initiation.
    /// @param service The service the staker unsubscribes from.
    /// @return unsubscribableFrom Timestamp when the unsubscription can be finalized.
    function initiateUnsubscribe(uint256 service) external returns (uint40 unsubscribableFrom);

    /// @notice Finalizes unsubscription from a service. Needs to be called after `initiateUnsubscribe`.
    /// @notice Cannot be called while the staker is frozen.
    /// @dev Calls `onFinalizeUnsubscribe` on the service. The service cannot prevent unsubscribing by reverting and forwarded gas is limited.
    /// @dev Calls `onUnsubscribe` on all lockers used by the service.
    /// @dev Emits `UnsubscriptionFinalizationWarning` if the callback to the service reverts.
    /// @dev Emits `Unsubscribed` on successful unsubscription finalization.
    /// @param service The service the staker unsubscribes from.
    function finalizeUnsubscribe(uint256 service) external;

    /// @notice Forcefully unsubscribes a staker from a service.
    /// @dev Called by the service the staker is subscribed to.
    /// @dev Calls `onUnsubscribe` on lockers used by the service.
    /// @dev Emits `Unsubscribed` on successful unsubscription.
    /// @param staker The staker to unsubscribe.
    function terminate(address staker) external;

    /// @notice Schedules a slasher update.
    /// @dev Called by the service that wants to update their slasher.
    /// @dev When a new slasher is scheduled, stakers subscribed to the service can unsubscribe from the service.
    /// @dev Emits `SlasherUpdateInitiated` on successful scheduling.
    /// @param newSlasher The new slasher address.
    /// @return scheduledTime Timestamp after which the slasher can be updated.
    function initiateSlasherUpdate(address newSlasher) external returns (uint40 scheduledTime);

    /// @notice Finalizes a slasher update. Must be called after `initiateSlasherUpdate`.
    /// @dev Emits `SlasherUpdated` on successful update.
    function finalizeSlasherUpdate() external;

    /// @notice Freezes a staker for performing a provable malicious action.
    /// @dev A frozen staker cannot take any action until the freeze period ends.
    /// @dev A service can freeze a staker once per freeze period.
    /// @dev Called by the slasher contract.
    /// @dev Emits `StakerFrozen` on successful freezing.
    /// @param staker The staker to freeze.
    function freeze(address staker) external;

    /// @notice Slashes a staker for performing a provable malicious action.
    /// @dev The staker needs to be frozen by the service before slashing.
    /// @dev Called by slasher the slasher contract.
    /// @dev Calls `onSlash` on all lockers used by the service.
    /// @dev Emits `StakerSlashed`.
    /// @param staker The staker to slash.
    /// @param percentages Percentage of funds to slash. Must specify percentages for all lockers. The percentages must be ordered by locker ID in ascending order. If `0` is passed for a locker, slashing is skipped for that locker. The sum of slash percentages within a freeze period cannot exceed the configured maximum slash percentage.
    function slash(address staker, uint8[] calldata percentages) external;

    /// @notice Checks whether a staker is frozen.
    /// @param staker The staker to check.
    /// @return frozen Whether the staker is frozen.
    function isFrozen(address staker) external view returns (bool frozen);
}
```

## Locker

Lockers should manage one specific asset at a time. Stakers must be able to deposit and withdraw this asset into the locker. Lockers act as the single source of truth for the underlying deposited balances and the voting power of stakers within services. Therefore a locker must keep track of the services a staker is subscribed to and the total supplies of the assets restaked to every service. Services should not be notified by lockers when balance changes happen; instead, services can listen to balance change events off-chain and act accordingly.

While a staker is frozen by the staking hub, a staker must not be able to deposit or withdraw assets. To prevent a staker from performing a malicious action and immediately withdrawing underlying assets, a locker must include a withdrawal queue. While assets are queued for withdrawal, they must remain slashable. During a freeze period, slashings should be aggregated (i.e., when two services slash 50% of the stake, the entire stake should be slashed). Slashed tokens should be burned.

The locker may perform risk analysis, limiting the number of services a staker can subscribe to based on certain parameters such as contagion risk, for example.

```solidity
// SPDX-License-Identifier: MIT OR Apache-2.0
pragma solidity 0.8.24;

/// @title Locker Interface
/// @author Polygon Labs
/// @notice A locker is responsible for managing a single asset within the Polygon Staking Layer. Lockers are used by stakers to subscribe to services that utilize the locker.
interface ILocker {
    /// @notice Indicates a change in underlying balance. This event should be monitored by services off-chain to keep track of the voting power of a staker.
    /// @dev Must be emitted on all balance changes (depositing, initiating withdrawal, slashing, etc.).
    /// @param staker The staker whose balance changed.
    /// @param newBalance The new balance of the staker.
    event BalanceChanged(address staker, uint256 newBalance);

    /// @dev Should be emitted when a staker withdraws from the locker.
    /// @param staker The staker who withdraws their balance or parts of it.
    /// @param amount The amount withdrawn.
    event Withdrawn(address staker, uint256 amount);

    /// @notice Called by the Staking Hub when a staker subscribes to a service. Must perform internal accounting such as updating the total supply of assets restaked to the service. May perform risk management.
    /// @param staker The staker subscribing to the service.
    /// @param service The service being subscribed to.
    /// @param maxSlashPercentage Maximum percentage that can be slashed from the staker's balance.
    function onSubscribe(address staker, uint256 service, uint8 maxSlashPercentage) external;

    /// @notice Called by the Staking Hub when a staker unsubscribes from a service. Must perform internal accounting such as updating the total supply of assets restaked to the service.
    /// @param staker The staker unsubscribing from the service.
    /// @param service The service being unsubscribed from.
    /// @param maxSlashPercentage Maximum percentage that can no longer be slashed from the staker's balance.
    function onUnsubscribe(address staker, uint256 service, uint8 maxSlashPercentage) external;

    /// @notice Called by the Staking Hub when a staker is slashed. The locker must burn the slashed funds. The locker should aggregate slashings by applying the percentage to the balance at the start of the freeze period. The locker must burn funds scheduled for withdrawal first.
    /// @dev Emits `BalanceChanged`.
    /// @param staker The staker being slashed.
    /// @param service The service slashing the staker.
    /// @param percentage The percentage of the staker's balance being slashed.
    /// @param freezeStart The freeze period id used to snapshot the stakers balance once at start of freeze period for slashing aggregation.
    function onSlash(address staker, uint256 service, uint8 percentage, uint40 freezeStart) external;

    /// @return The id of the locker.
    function id() external view returns (uint256);

    /// @return amount The amount of underlying funds of the staker deposited into the locker.
    function balanceOf(address staker) external view returns (uint256 amount);

    /// @return amount The amount of underlying funds of the staker deposited into the locker and restaked to the service.
    function balanceOf(address staker, uint256 service) external view returns (uint256 amount);

    /// @return votingPower The representation of the voting power of the stakers underlying balance.
    function votingPowerOf(address staker) external view returns (uint256 votingPower);

    /// @return votingPower The representation of the voting power of the stakers underlying balance within a service.
    function votingPowerOf(address staker, uint256 service) external view returns (uint256 votingPower);

    /// @return The total supply of underlying funds of all stakers deposited into the locker.
    function totalSupply() external view returns (uint256);

    /// @return The total supply of underlying funds of all stakers deposited into the locker and restaked to the service.
    function totalSupply(uint256 service) external view returns (uint256);

    /// @return The total representation of the voting power of all stakers underlying balances.
    function totalVotingPower() external view returns (uint256);

    /// @return The total representation of the voting power of all stakers underlying balances within a service.
    function totalVotingPower(uint256 service) external view returns (uint256);

    /// @return The services the staker is subscribed to.
    function services(address staker) external view returns (uint256[] memory);

    /// @return Whether staker is subscribed to service.
    function isSubscribed(address staker, uint256 service) external view returns (bool);
}
```

## Service

Services can represent any network that requires coordination of stakers towards a common goal secured by deposited stake, from proof-of-stake networks or sequencer networks to applications like oracle networks. Services are notified by the Staking Hub whenever a staker tries to subscribe to it. This allows the service to verify the staker fulfills any relevant conditions such as minimum balances, submitting a BLS key beforehand, or locking in their subscription for a certain amount of time.

Unsubscription is split into two steps. First, a staker needs to initiate their unsubscription. This allows a service to wrap up any tasks currently pending for that staker and then exit them from the network. As the second step, the staker can finalize their unsubscription. When a staker unsubscribes from a service and the staker is still locked in, the service has the opportunity to prevent an unsubscription by reverting. To prevent a malicious or compromised service holding stakers’ assets hostage, after the lock in period expires, the staker can unstake at any time. The service is still notified on unsubscription initiation and finalization to perform any tasks necessary.

```solidity
// SPDX-License-Identifier: MIT OR Apache-2.0
pragma solidity 0.8.24;

/// @title Service Interface
/// @author Polygon Labs
/// @notice A service is a contract that manages applications within the Polygon Staking Layer.
interface IService {
    /// @notice Called by the Staking Hub when a staker subscribes to the service.
    /// @dev May perform checks, such as minimum stake requirements, etc. and revert if the staker is not eligible to subscribe.
    /// @param staker The staker subscribing to the service.
    /// @param lockingInUntil The time until the staker is locked in.
    function onSubscribe(address staker, uint256 lockingInUntil) external;

    /// @notice Called by the Staking Hub when a staker initiates unsubscription from the service.
    /// @dev If the staker is locked in, the service can prevent unsubsctiption by reverting.
    /// @dev If the staker is not locked in, the forwarded gas is limited and the service should not revert.
    /// @param staker The staker initiating unsubscription from the service.
    /// @param lockedIn Indicates if the staker is locked in.
    function onInitiateUnsubscribe(address staker, bool lockedIn) external;

    /// @notice Called by the Staking Hub when a staker finalizes unsubscription from the service.
    /// @dev The service should not revert and forwarded gas is limited.
    /// @param staker The staker finalizing unsubscription from the service.
    function onFinalizeUnsubscribe(address staker) external;
}
```

## Slasher

Slashers are registered with the Staking Hub on behalf of a service and allow it to slash a staker. The slashing contract may be set to the service contract; however it’s recommended that the slasher is set to a separate immutable contract. The slasher contract must call the freeze function on the staking hub first prior to slashing. Some form of proof should be passed to the function that initiates the freezing period to ensure that stakers cannot be frozen and slashed arbitrarily. After a staker has been frozen, the staker can be slashed any number of times, as long as the total slashed percentage of each locker does not exceed the defined maximum slashing percentages. Once `slash` is called on Staking Hub, the slashing is finalized. Therefore, slashers may implement additional functionality, such as slashing accumulation or innocence proving, before calling Staking Hub.

This is an example of the slasher interface:

```solidity
// SPDX-License-Identifier: MIT OR Apache-2.0
pragma solidity 0.8.24;

/// @title Slasher Interface Example
/// @author Polygon Labs
/// @notice A Slasher separates the freezing and slashing functionality from a Service.
/// @dev this is an example interface for a slashing contract
interface ISlasher {
    /// @notice Temporarily prevents a Staker from taking action.
    /// @notice Provides proof of malicious behavior.
    /// @dev Called by a service.
    /// @dev Calls freeze on the Hub.
    function freeze(address staker, bytes calldata proof) external;

    /// @notice Slashes a percentage of a Staker's funds.
    /// @notice The Staker must be frozen first.
    /// @dev Called by [up to the Slasher to decide].
    /// @dev Calls slash on the Hub.
    function slash(address staker, uint8[] calldata percentages) external;
}
```

# Backwards Compatibility

This PIP takes into consideration the use of [ERC20](https://github.com/ethereum/ERCs/blob/master/ERCS/erc-20.md) and [ERC721](https://github.com/ethereum/ERCs/blob/master/ERCS/erc-721.md) standards in Strategies.

It does not replace any existing infrastructure and is therefore not incompatible with any earlier developments.

# **Reference Implementation**

See [GitHub](https://github.com/0xPolygon/staking-hub/tree/dev/src).

# Security Considerations

1. Malicious stakers can avoid getting frozen and slashed if the unsubscription notice is not long enough by initiating and finalizing unsubscription before the service detects the malicious action and has the chance to freeze them.
   1. On initiating an unsubscription, the lockers will be notified of the unsubscription; therefore, the service should also consider the staker as unsubscribed to prevent a situation where the staker performs malicious actions and immediately finalizes the unsubscription.
   2. It is recommended for the defined notice period to be sufficiently longer than the withdrawal delay within a locker, to ensure that funds can be frozen should a staker perform a malicious action during the withdrawal period.
2. Slashing percentages must be specified for all lockers and ordered by locker IDs. Otherwise, the slashing may be inadequate and result in inaccurate slashings.
   1. If a locker reverts on slashing, `0` can be passed as the slashing percentage to skip the locker. This prevents a locker being able to revert and prevent slashings on other lockers.
   2. Slashers need to keep in mind that the slashing percentage will be applied to the balance at the start of the freeze period.
3. Slasher contracts are self-regulating mechanisms; therefore, stakers put trust in their functionality (freezing, slashing, etc.). Stakers should do due diligence on slashers before subscribing to a service.
   1. Slashers that use an upgradeable proxy, could be upgraded at any time and change the rules of the slashing. An attacker could compromise the proxy, upgrade the contract and slash every staker. Therefore it’s recommended to use immutable contracts for slashers.
   2. An immutable slasher can be updated with the Staking Hub. There is a slasher update timelock in place, and stakers can unsubscribe if they do not trust the new slasher.
4. Lockers are the most critical piece of the Staking Layer besides the Staking Hub. Stakers completely entrust their funds to lockers. Services trust lockers to report balances and voting power correctly, and to not revert maliciously.
5. A compromised locker can prevent services from working correctly by preventing subscriptions and unsubscriptions. Stakers would still be able to withdraw funds from other lockers the services use. However a compromised locker would result in stolen funds anyway. But the services would remain able to slash them at any point in the future.
   1. A malicious service that uses a malicious locker can prevent unsubscription from itself and trap a staker. The service would be able to slash them in any locker it uses, at any point the future.
6. Stakers should do due diligence on services before subscribing. This is because the only functionalities enforced by the Staking Hub are the slasher, max slash percentages, lockers, the lock-in period for the staker, and unsubscription notice period (all immutable except the slasher). Everything else is decided by the service contract on actions (e.g. on unsubscribing).
7. Stakers can withdraw funds from a locker at any time. Lockers must add a withdrawal delay and emit the `BalanceChanged` event so services can monitor stakers’ actions and act accordingly (e.g., freeze, slash, terminate).
   1. Slashing must also result in a `BalanceChanged` event.
   2. Because the lock-in is not enforced on withdrawing, a large amount of stakers (e.g., validators) can withdraw at the same time, affecting network health/uptime.
8. Stakers must not be able to take any action while frozen. Otherwise, other actions may collude with slashing, etc. Staking Hub enforces this on the hub level, but Lockers must enforce it as well.
9. Lockers should burn slashed funds. In any case, the punished staker must somehow lose slashed funds.
   1. By default, lockers are supposed to burn funds. That way, services don’t benefit directly by slashing a staker, while the staker gets punished by losing stake. As such, when multiple slashers may want to slash more than 100% through slashing aggregation, no funds are lost because there isn’t any gain for any party. This also ensures the staker cannot retrieve the funds by slashing themselves through a service they control.
   2. Locker must burn funds scheduled for a withdrawal first. Otherwise, different actions (e.g. withdrawing, slashing, etc.) may collude.
10. Because funds are restaked, slashing could have cascading effects, wherein a staker gets slashed by service A, falling below a minimum balance requirement threshold for service B, which then slashes them as well.
11. Lockers must update the stakers balance and voting power on withdrawal initiation, not finalization to prevent stakers from performing malicious actions and immediately finalizing the withdrawal.
12. Lockers, services, and slashers must have proper access control in place (e.g. only allowing calls from the Staking Hub).
    1. When services are upgradeable, they should use multi-signatures, DAO governance, or other signing mechanisms for critical actions such as upgrading.
13. The staker cannot be unfrozen manually. They need to wait until the freeze period ends to perform any further actions.
14. A staker remains subscribed to a service until initiating an unsubscription (or the subscription terminated). This means the stakers stake in a subscribed service may be zero.
15. The Staking Hub does not manage restaking risk. Lockers should perform risk management. For example, a locker may not allow the staker to subscribe to a service if the sum of all max slash percentages of the services the staker is subscribed to that use the locker would exceed 100% with the new subscription. Restaking limits should be monitored off-chain for risks and adjusted accordingly through governance mechanisms.
