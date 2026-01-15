---
PIP: 69
Title: Full ERC-20 Functionality for Validator Share Tokens
Description: Enable full ERC-20 functionality to simplify creation of POL liquid staking tokens (LSTs) and expand the utility of staked POL.
Author: Pete Kim
Discussion: https://forum.polygon.technology/t/pip-69-full-erc-20-functionality-for-validator-share-tokens/21162
Status: Executed
Type: Contracts
Date: 2025-07-21
---

## Abstract

This proposal outlines an upgrade to Polygon’s staking system to give **validator share tokens** full ERC-20 functionality. Currently, when users delegate POL tokens to a validator, they receive validator-specific share tokens that represent their stake. However, these share tokens are non-tradable – in particular, the `approve` function is [disabled](https://etherscan.io/address/0x7e94d6cabb20114b22a088d828772645f68cc67b#code) – which makes them unusable in DeFi smart contracts. By removing this limitation and implementing standard ERC-20 features (including the `permit` extension for gasless approvals per [ERC-2612](https://eips.ethereum.org/EIPS/eip-2612)), we enable share tokens to be freely transferred and integrated into other contracts. This change will make the creation of liquid staking derivatives (LSTs) for POL trivial, unlocking liquidity for currently dormant staked tokens. Ultimately, the goal is to vastly expand the utility of staked POL and boost liquidity in Polygon chain, and AggLayer chains such as Katana.

## Motivation

Currently, over one-third of all POL tokens (\~3.5 billion, about 33% of the supply) are staked in the Polygon PoS network – representing on the order of **hundreds of millions of dollars (\~$880M)** in value. This indicates a huge untapped opportunity for Polygon’s DeFi ecosystem: unlocking potentially **hundreds of millions in liquidity** currently tied up in staking.

One key reason for the lack of POL LST adoption to date is the **complexity and trust requirements** in existing solutions. Without a transferable share token standard, LST providers have had to create complex workflows and custodial setups. These approaches introduce friction and risk, limiting user adoption.

By contrast, if Polygon’s native **validator share tokens were fully ERC-20 compliant**, creating a POL liquid staking token could be as simple as deploying a smart contract that wraps these share tokens. **No special validator permissions or complex custody would be required.** Any user could delegate to a validator normally (through the stake manager) and receive standard ERC-20 share tokens representing their stake. A third-party protocol or even a simple open-source contract could then permissionlessly accept those tokens and mint a new unified LST. Because the native share tokens themselves encode the stake and reward balances, the LST contract’s job becomes trivial bookkeeping, rather than the heavy lifting of managing actual staking operations. In short, **making share tokens composable drastically lowers the barrier to creating and using POL LSTs**.

Enabling liquid staking for POL may also **increase staking participation** by reducing the opportunity cost (users can stake without sacrificing liquidity), thereby improving network security. 

## Specification

### Changes to `ValidatorShare` Token Contract

Each validator on Polygon has an associated `ValidatorShare` contract that issues ERC-20 [share tokens](https://github.com/maticnetwork/contracts/tree/eef53596046eda70a53653a8e5ff79b1cbf0a4f9/contracts/staking/validatorShare) to delegators. We propose the following modifications to the `ValidatorShare` contract implementation:

* **Enable Standard ERC-20 Approval:** Remove the inheritance of `ERC20NonTradable`. Currently `_approve()` is overridden to revert (`"disabled"`), which prevents use of `approve` and consequently `transferFrom`. After this change, the share token should behave like a normal ERC-20: holders can call `approve(spender, amount)` to grant allowances, and other contracts can call `transferFrom` on their behalf up to the approved amount. This change **restores the crucial `approve`/`transferFrom` functionality** that allows tokens to be used by smart contracts.
* **ERC-20 Metadata:** Implement the standard `name()`, `symbol()`, and `decimals()` functions for the share token:
  * `symbol()`: Return a symbol string in the format **`dPOL[ID]`**, where `[ID]` is the validator’s ID. For example, the share token for validator ID 1 would return `"dPOL[1]"`. This clearly identifies the token as “delegated POL” tied to a specific validator.
  * `name()`: Return a descriptive name such as **`Delegated POL [ID]`**. For validator 1, this would be `"Delegated POL [1]"`. This makes it easy in interfaces to recognize the token as POL staked with a given validator.
  * `decimals()`: Return **18**, matching the industry standard and the POL's decimal places.
* **Permit (ERC-2612):** Implement the `permit` function as defined in [ERC-2612](https://eips.ethereum.org/EIPS/eip-2612). This function allows a holder to approve an allowance via an off-chain signature, enabling **gasless approvals** and one-transaction deposit flows. By supporting permit, dApps can seamlessly integrate share tokens with improved UX (no need for a separate approve transaction from users).
* **Preserve Automatic Reward-Claim on Transfer:** Currently, whenever share tokens are transferred (e.g. when a delegator unstakes or moves tokens), the contract triggers a claim of any pending rewards for both the sender and recipient addresses. This behavior should be **retained** for all transfers, whether initiated by `transfer` or `transferFrom`. In practice, this means the overridden `_transfer` function in `ValidatorShare` will continue to call the internal reward withdrawal routine for the sender and receiver before updating balances. Preserving this ensures that rewards aren’t “left behind” or unevenly distributed when shares move between addresses – it maintains the invariant that each address’s share balance corresponds to a fully updated claim on underlying POL at transfer time. **Any smart contract that accepts deposits of share tokens MUST take this into account and handle balance changes in POL**.

Aside from the above changes, the core logic of `ValidatorShare` remains the same. After this proposal, each validator’s share token will simply be a **fully-fledged ERC-20**, with the added convenience of `permit`. 

## Rationale

### Why enable transfers and approvals now?

When Polygon’s staking contracts were originally designed, validator share tokens were made non-transferrable avoid potential complications of potential fraud by a rogue validator and secondary trading. The share tokens were meant to be an invisible implementation detail. The very first implementation did not even allow simple transfers between EOAs, which was later enabled to allow key rotations. Given the maturity of Polygon’s ecosystem and its decentralized validator network, and the community’s interest in liquid staking, the benefits of making share tokens composable now clearly outweigh the original reasons for non-transferability.

## Backwards Compatibility

From a protocol perspective, these changes are mostly additive. Existing delegators will simply find that their `dPOL` share tokens are now composable. If a user takes no action, their stake is unaffected – they continue earning rewards as before.

### Contract Upgrade

A new `ValidatorShare` implementation contract will need to be deployed and the existing `ValidatorShareProxy` contract for each validator should point to the new implementation. The upgrade must ensure that all existing validator share contracts receive the new logic and the new implementation must ensure that the original storage layout remain unaffected.

### Decimal Places

The previous contract lacked a `decimals` function, causing wallet frontends to display a huge number. This behavior only affects frontend display; the balance function will continue returning the same value after the upgrade. While this change is unlikely to impact existing integrations, it is noted here for completeness.

## Security Considerations

Converting share tokens into freely transferable ERC-20s introduces new vectors to consider, mostly analogous to the risks of any fungible token in DeFi, but some specific to staking:

* **Allowance Misuse:** Once `approve` is enabled, share token holders must exercise caution just as with any ERC-20 – approving a malicious contract could put their tokens at risk. If an attacker tricks a user into approving a large amount, the attacker could call `transferFrom` to take the user’s staked tokens (i.e., their share tokens). Users and dApps should treat share tokens with the same security precautions as they do POL itself and other ERC-20 tokens.
* **Slashing and Risk Exposure:** Although slashing is currently disabled for Polygon staking, it could be enabled in the future. If that happens, LSTs derived from validator share tokens may need to socialize any resulting losses among holders, similar to how other LSTs handle such events.
* **Governance:** An LST smart contract may accumulate a significant amount of delegated POL, potentially giving it substantial influence in a token-based governance system. Any such governance mechanism should account for this possibility and ensure that voting rights are attributed to the LST holders, rather than the contract itself.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
