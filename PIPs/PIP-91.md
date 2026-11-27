---
PIP: 91
Title: Permissioned Validator Entry via ValidatorPass
Authors: Adam Dossa, Agustin Aguilar
Description: Gates new validator admission behind single-use, expiring, key-bound passes issued under authority delegated by the Protocol Council
Discussion: https://forum.polygon.technology/t/permissioned-validator-entry-via-validatorpass/22108
Status: Peer Review
Type: Core
Date: 2026-08-18
---

## Abstract

This proposal introduces an on-chain permissioning gate for new validator entry on Polygon PoS. A minimal hook is added to the L1 StakeManager: before any new validator can join the active set, an optional module — resolved from the Registry under keccak256("validatorPass") — must consume a single-use, expiring pass bound to the validator's address and consensus signing key.

Passes are issued by holders of a PASS_ISSUER_ROLE. The role is administered by PoS governance, which is controlled by the Protocol Council. The Council holds ultimate authority and may delegate day-to-day pass issuance to any operator of its choice and may revoke that delegation at any time. For convenience Polygon Labs has agreed to serve as operator until a new operator is selected.

If no module is registered, entry remains permissionless — the mechanism is fully backward compatible and reversible.

## Motivation

PIP-39 established a validator admission framework requiring prospective validators to identify themselves and pass an objective scoring process before joining the active set. However, PIP-39 is enforced only socially: at the smart-contract level, any address holding sufficient POL can call stakeFor and claim a free validator slot the moment one opens.

This gap has produced concrete problems:

* **Unidentified validators.** Validators occupy a privileged role in the protocol — they sign checkpoints and produce blocks. Several slots have been claimed by operators with no published identity and no completed admission process, in violation of PIP-39. An unidentified operator in the active set is a significant and unquantifiable security risk.

* **Slot sniping.** When a slot is vacated (e.g., when a non-compliant validator is offboarded), it can be immediately re-claimed by another unknown party, undoing the offboarding. Defending against this currently requires manually racing to fill open slots with placeholder stakes — an operationally fragile and capital-intensive workaround.

* **Governance load.** Without a delegation mechanism, every admission decision would require a direct governance transaction from the Protocol Council, whose cadence is not suited to routine onboarding operations.

This proposal closes the enforcement gap by making PIP-39 compliance technically checkable at the point of entry, while keeping ultimate authority with the Protocol Council and keeping the mechanism removable by a single governance action.

## Rationale

The chosen design keeps a complete public on-chain audit trail of every approval, revocation, and redemption; automatic expiry of stale approvals; per-candidate revocability; binding of the consensus signing key at approval time; and a delegation model that removes the Protocol Council from the per-admission critical path without ceding ultimate control.

Two further design choices are worth noting:

* The pass binds the validator address and consensus key, not the funder. Any account may pay the stake (preserving stakeFor's third-party funding design, e.g. custodial or treasury-funded setups), but only the exact consensus key committed at issuance can enter. This provides the security benefit of key binding without prohibiting delegated staking.

* The gate is policy-agnostic and swappable. The StakeManager only calls consumePass(...) on whatever module the Registry points at. Admission policy can evolve (or be removed entirely) by re-pointing a single Registry key — no further StakeManager upgrade required.

## Specification

#### 1. StakeManager gate

StakeManager._stakeFor (the common internal path for both MATIC and POL entry) is extended with a permissioning check before any funds move:

```solidity
// Registry key of the optional validator-pass module that permissions new validator entry.
bytes32 constant VALIDATOR_PASS_KEY = keccak256("validatorPass");

// Inside the staking entry path, after the slot and minimum-deposit checks:
address validatorPass = Registry(registry).contractMap(VALIDATOR_PASS_KEY);
if (validatorPass != address(0)) {
    require(
        IValidatorPass(validatorPass).consumePass(user, signerPubkey, amount, msg.sender),
        "no valid pass"
    );
}
```

Properties:

* **Unset ⇒ permissionless.** If the Registry entry is address(0), behavior is identical to today.
* **Atomic.** The consume happens inside the staking transaction; a downstream revert rolls the pass consumption back.
* **Entry-only.** Existing validators are unaffected: restaking, delegation, signer rotation, fee top-ups, and exit are NFT-gated paths that never reach the gate.
* **Full context forwarded.** The interface forwards the validator, consensus key, stake amount, and funder, so future policy modules can gate on any of these; the initial module uses only the validator and key.

#### 2. IValidatorPass interface

```solidity
interface IValidatorPass {
    /// @param validator    Prospective validator (the `user` of `stakeFor`).
    /// @param signerPubkey Consensus key supplied to `stakeFor`.
    /// @param amount       Stake amount the entrant is joining with.
    /// @param funder       `msg.sender` of the stake call (third-party entry allowed).
    /// @return consumed    True if a valid pass was consumed (entry permitted).
    function consumePass(address validator, bytes calldata signerPubkey, uint256 amount, address funder)
        external
        returns (bool consumed);
}
```

#### 3. ValidatorPass module

A new ValidatorPass contract implements the pass ledger:

* **Single-use, expiring, key-bound passes.** A pass records keccak256(signerPubkey) (the committed 64-byte consensus key) and an expiration timestamp. Redemption requires the exact committed key and must occur before expiry. Each validator holds at most one pass; re-issuing supersedes (and implicitly revokes) any unredeemed pass.
* **Lifecycle with full audit trail.** Passes move through Active → Consumed (on stake) or Active → Revoked (by an issuer). Terminal states are marked rather than deleted, and every transition emits an event (PassIssued, PassRevoked, PassRedeemed), so the on-chain log carries the complete authorization history.
* **Only the StakeManager can redeem.** consumePass reverts for any caller other than the canonical StakeManager (resolved via the Registry), and reverts with explicit errors (NoPass, PassExpired, WrongSignerKey, etc.) on any failed redemption.
* **Enumerable.** Current pass holders are enumerable on-chain for transparency (passHolders(), hasActivePass(validator)).

#### 4. Authority and delegation model

Role administration uses OpenZeppelin AccessControl:

| Role | Held by | Powers |
|------|---------|--------|
| DEFAULT_ADMIN_ROLE | PoS Governance (Protocol Council) | Grant / revoke PASS_ISSUER_ROLE |
| PASS_ISSUER_ROLE | Governance at deploy; delegable | issuePass, revokePass |

The intended operating model:

* The Protocol Council retains ultimate authority: it administers the issuer role, can act as an issuer directly, can revoke any delegate at any time, and can disable the entire gate by clearing the Registry entry.
* The Council delegates PASS_ISSUER_ROLE to Polygon Labs, which operates the PIP-39 admission process (identity verification, scoring, review) and issues passes to approved candidates as slots become available.
* Approvals remain individually visible and revocable on-chain regardless of who issued them.

#### 5. Admission flow (end to end)

| Step | Actor | Action |
|------|-------|--------|
| 1 | Candidate | Completes the PIP-39 admission process off-chain (identity, scoring) |
| 2 | Issuer (Polygon Labs, under delegated authority) | Reviews; calls issuePass(validator, signerPubkey, expiration) |
| 3 | Candidate (or a third-party funder) | Calls stakeFor with the committed key within the expiry window |
| 4 | StakeManager | Resolves the module, consumePass succeeds, stake proceeds; PassRedeemed emitted |

The existing validatorThreshold slot cap continues to apply on top of the gate.

#### 6. Activation

Deployment and activation are governance actions, expected to follow the PIP-50 signalling process:

1. Deploy ValidatorPass with the Governance contract as admin and initial issuer.
2. Upgrade the StakeManager implementation to include the gate (no storage layout changes).
3. Governance transaction: set Registry.contractMap(keccak256("validatorPass")) to the module address — entry becomes permissioned.
4. Governance transaction: grant PASS_ISSUER_ROLE to the designated Polygon Labs issuer address.

Deactivation at any time: reset the Registry entry to address(0) — entry reverts to permissionless with no further changes.

## Backward Compatibility

* The stakeFor / stakeForPOL calling conventions are unchanged; no off-chain tooling changes are required for candidates beyond holding a valid pass.
* With the Registry entry unset, behavior is bit-for-bit identical to the current permissionless system.
* Existing validators and all post-entry operations (delegation, signer rotation, restaking, unstaking, rewards) are unaffected.
* The change is an L1 contract upgrade only; no hard fork of Bor/Heimdall is required.

## Security Considerations

* **Key binding.** Because stakeFor lets msg.sender supply the consensus key, a naive address-allowlist would allow an unvetted party to insert their own signing key under an approved address by funding the stake first. Binding the key at issuance eliminates this vector. Note the binding gates entry only: post-entry signer rotation remains an NFT-gated validator operation, consistent with validators' existing self-service control.
* **Issuer key compromise.** A compromised issuer key could authorize arbitrary entrants until revoked. Mitigations: every issuance is an on-chain event (detectable immediately), issued-but-unredeemed passes are individually revocable, governance can revoke the issuer role or clear the Registry gate in one transaction, and the validatorThreshold cap bounds the number of admissible validators regardless.
* **Centralization trade-off.** This proposal intentionally trades open entry for enforceable admission, motivated by the security risk of unidentified privileged operators. The trade-off is bounded: the authority chain is Protocol Council → delegate, fully on-chain and reversible; the admission criteria remain those of PIP-39, already ratified by governance; and removal of the gate requires only a single governance action.
* **Existing privileged paths.** The owner-only insertSigners and migrateValidatorsData paths sit outside the gate and remain governance-controlled; they are unchanged by this proposal.
* **Audit.** The StakeManager gate (~15 lines) and the ValidatorPass module will undergo security review and audit before mainnet activation, including mainnet-fork upgrade rehearsals.

## References

* PIP-4: Validator Performance Management
* PIP-39: Validator Admissions into PoS Network
* PIP-50: Staked Tokenholder Signalling
* Implementation: pos-contracts#52 (StakeManager gate), pos-contracts-v2#3 (ValidatorPass module)

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
