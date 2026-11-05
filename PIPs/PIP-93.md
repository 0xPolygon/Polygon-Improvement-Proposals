---
PIP: 93
Title: Polygon Security Council Consolidation
Authors: Christopher von Hessert
Description: Consolidates the Protocol Council, PoS, and AggLayer multisigs into a single Security Council with a Polygon Labs proposer route and a 7-day timelock
Discussion: TBD
Status: Draft
Type: Contracts
Date: 2026-11-02
---

## Abstract

This PIP reorganizes the Polygon Protocol Council into a **Polygon Security Council**: eleven signers drawn from ten organizations, operating a single **8-of-11** signature policy across Ethereum and Polygon.

It consolidates the Protocol Council, Polygon PoS, and AggLayer multisigs into one governance structure per network. Routine changes are proposed by a Polygon Labs multisig and executed after a **7-day timelock**. The Security Council retains the power to **veto** any proposed change during that window, to **execute immediately** without the timelock, and to **unpause** contracts.

This proposal supersedes PIP-29 and should be read in conjunction with PIP-54, PIP-67, PIP-68, and PIP-77.

## Motivation

### The pace of change has outrun the structure

PIP-29 was written in October 2023. The 7/13 and 10/13 routes were sized for a development cadence that no longer exists.

Two things changed. First, the rate at which vulnerabilities and bugs are found has risen sharply. Automated analysis, agentic fuzzing, and LLM-assisted review have compressed the time between a bug existing and a bug being found, by defenders and by attackers alike, and the number of fixes that need to reach mainnet has grown with it. Second, the bridge and staking contracts are in a sustained period of improvement and modernization. On top of the fixes, that work produces a steady queue of upgrades to the same contracts.

Both trends land on the same group of signers. Under the current structure, every fix and every upgrade requires assembling a quorum of Council signatures, so signer workload grows in step with the volume of change. A structure that takes days to assemble signatures for a routine parameter change is not a security control, it is a delay that we pay for on every upgrade and again on every mitigation. Closing vulnerabilities at the speed they are now found, and shipping the bridge and staking work on schedule, requires a route that does not depend on a signing ceremony for each change.

### Operational burden

Polygon network is subject to several council structures across multiple multisigs, covering the Polygon PoS chain, AggLayer, and the various staking and bridge contracts. Each carries its own signer set, threshold, and operating procedure, with overlapping membership between them.

The practical effect is that every upgrade has to be designed around the various approval mechanisms that govern the applicable smart contract, and a single protocol change can require separate signing ceremonies across separate groups. Each additional multisig is another set of keys to manage, signers to vet, and Safes to monitor. Unifying them into one structure reduces the number of targets and concentrates protection on fewer, better-defended signing setups.

This proposal adds a second route rather than replacing the first. The Polygon Labs Operations multisig can submit fixes and upgrades through the timelock, so routine work no longer has to wait on a Council signing ceremony. The Security Council keeps all of its existing powers: it can submit any transaction itself, veto anything in the timelock queue, and execute immediately when a change cannot wait. Every transaction from Polygon Labs is visible for the full timelock window before it executes, and signers can spend that time reviewing the queue rather than coordinating signatures for each change.

### Signer safety

PIP-29 committed to "13 publicly-named members." That structure was reasonable practice in 2023, but is now inefficient when compared to updated market practice.

Named multisig signers are a targeting list. Publishing which individuals hold keys to contracts securing significant value exposes those individuals to social engineering, physical coercion, and legal pressure, and it does so for a transparency benefit that is served just as well at the organizational level. Several major protocols have moved away from individual disclosure in recent years for exactly this reason.

## Specification

### Composition

The Security Council consists of **eleven signers** drawn from **ten organizations**, with Polygon Labs holding two seats and every other organization holding one.

Current Protocol Council Organizations:

- Polygon Labs
- Sherlock
- OpSek
- Hexens
- Sigma Prime
- Curve
- QuickSwap
- EthCC
- L2Beat
- Coinbase

Consistent with PIP-67, the two Polygon Labs seats are held by internal nested-multisigs rather than by individuals.

**No organization may hold more than two seats.** Any change to the membership or to this cap requires Council Review and Approval, executed as a Security Council action under the signature policy below.

### Signature policy

**8 of 11** for all Security Council actions, on all contracts, on both Ethereum and Polygon. The two-route threshold structure established by PIP-29 is retired.

### Roles

**Polygon Labs Operations multisig (5/9).** Composed of Polygon Labs Security and Engineering personnel. It may:

- propose transactions to the timelock, including contract upgrades and administrative changes;
- pause contracts.

It may not execute any transaction that bypasses the timelock, and it may not unpause contracts.

**Security Council (8/11).** It may:

- **veto**, cancelling any proposed transaction during the timelock window;
- **execute immediately**, applying a change without waiting out the timelock;
- **unpause** contracts;
- **submit** any transaction directly, including contract upgrades and changes to administrative parameters.

**Timelock (7 days), deployed on Ethereum and Polygon.** All transactions proposed by the Polygon Labs Operations multisig are subject to a 7-day delay before execution. The Security Council may cancel during the window.

```
Polygon Labs Ops (5/9)  --proposes-->  Timelock (7 days)   --executes-->  Contracts
                                              |
                                    Security Council (8/11)
                                    VETO  or  IMMEDIATE EXECUTION
```

### Scope

The Security Council assumes upgradeability and administrative rights, including pause and unpause, over the contracts currently governed by:

- the Polygon Protocol Council (PIP-29, as extended by PIP-54 and PIP-68);
- the Polygon PoS Council;
- the AggLayer / zkEVM Security Council.

### Disclosure

Polygon Labs will publish and maintain the list of organizations holding Security Council seats. **Individual signer identities and signer addresses will not be published.** Organizations may disclose their own participation at their discretion.

### Implementation

**Phase 1.** Apply the eleven-signer set to all existing multisigs in scope, and reassign routine operational and pause rights to the Polygon Labs Operations multisig. No contract deployments. Each Safe executes its owner change at its current threshold, in a published order, one at a time.

**Phase 2.** Deploy the timelock contracts on Ethereum and Polygon, wire the Polygon Labs Operations multisig as proposer, and consolidate the remaining multisigs into one structure per network.

## Rationale

### Why 8 of 11

The threshold has to hold on two opposing axes. Raising it makes collusion harder, because more signers must be compromised to force a malicious change. Raising it also makes obstruction easier, because fewer signers are needed to block a legitimate one. A council that cannot act is a different failure from a council that acts wrongly, and both are real.

At eleven seats, eight signatures gives:

| Property | Value |
| :---- | :---- |
| Signer threshold | 8/11 = **72.7%** |
| Signers required to block an action | **4** |
| Signers who may be unavailable before the Council is stalled | 3 |
| Distinct organizations | 10 |
| Organizations required to reach 8 signatures | 7 (Polygon Labs plus six others) |
| Organization threshold | 7/10 = **70.0%** |

The second ratio is the one most proposals miss. Where a single entity holds multiple seats, those seats collapse to one entity for the purpose of assessing decentralization, so the organization threshold rather than the signer threshold is the meaningful measure of independence.

Eight of eleven was selected because it holds a supermajority on both measures while keeping the blocking minority at four rather than three. Four signers to block means an attacker seeking to prevent the Council from responding must reach one more organization than at a smaller council, and three signers may be travelling, ill, or mid key-rotation without the Council losing the ability to act. Base operates its Security Council at the same 8-of-11 policy.

**Polygon Labs is capped at two seats for a structural reason, not a political one.** Four signers block any action. Three Polygon Labs seats would put Polygon Labs within one signature of a unilateral veto over the body whose purpose is to check it, and would drop the organization threshold to 6 of 9.

Eleven seats rather than thirteen reflects the narrowed mandate. The Council no longer has to sign routine traffic, though it can still originate any change itself. Removing that obligation is what makes a supermajority threshold operationally viable at all.

### Why Polygon Labs proposes

Separating the proposer from the reviewer is the dominant pattern among comparable systems. It puts the party with the most operational context in the position to originate changes, and the party with the most independence in the position to stop them.

It also removes the incentive that created the 7/13 route. A council that must sign routine traffic will always be under pressure to lower its threshold. A council that is not required to sign routine traffic, but keeps the power to originate any change itself, can hold a high one.

### Why the timelock is 7 days

Seven days shortens the existing PIP-29 regular-route delay by three days. That is a direct contribution to the pace problem set out above: every routine change currently carries ten days of latency whether or not anyone intends to review it. Seven days also remains at or above the exit window generally expected where an entity outside the Security Council can initiate an upgrade.

Seven rather than five because a seven-day window always contains a full working week. No proposal can be queued and executed without at least five business days of visibility, regardless of which day it is submitted. Five days does not guarantee that; a Thursday submission would clear over a weekend.

### Why organization-level disclosure

The purpose of the disclosure requirement is to let observers verify that the Council is genuinely diverse and not captured. A published list of ten independent organizations establishes that. Publishing which individual at Hexens or Sigma Prime holds the key adds nothing to that verification and adds material personal risk for the individual.

Starknet's Security Council operates on this basis, publishing member organizations without individual names. The relevant failure case is publishing nothing at all, which is not what is proposed here.

## Backward Compatibility

This proposal supersedes the composition, signature policy, and route structure established in PIP-29 and amended by PIP-67 and PIP-77. It supersedes the "publicly-named members" commitment in PIP-29, and it supersedes the requirement in PIP-29 that changes to Council membership be governed by the PIP framework.

Contract addresses are preserved wherever possible. Phase 1 changes multisig ownership in place rather than migrating to new Safes, so integrations referencing existing addresses continue to resolve. Phase 2 introduces new timelock contracts and changes the owner of contracts in scope; addresses affected will be published before execution.

Members of the current Protocol Council not continuing on the Security Council cease to hold signing rights on completion of Phase 1.

## Security Considerations

**Collusion.** Compromising the Council requires eight signers across at least seven independent organizations. Under the current 7/13 regular route it requires seven signers across seven organizations. This proposal raises the absolute bar by one signer and applies it to every action rather than only to emergencies.

**Blocking minority.** Four signers can block any Council action. Polygon Labs holds two seats and therefore cannot block alone, and cannot veto a proposal originating from its own Operations multisig without at least two external organizations agreeing.

**Immediate execution and the exit window.** The Council's immediate-execution power bypasses the timelock by design. This is the standard construction: a security council that must wait seven days to patch an actively exploited contract is not a security control. Because the power sits with the Council rather than with an outside party, it does not undermine the timelock's function as an exit window.

The Polygon Labs Operations multisig must hold no path that bypasses the timelock. If it did, the 7-day delay would be nominal and the proposer separation would provide no protection. This constraint is load-bearing and must be verified at deployment, not assumed.

**Operational slack.** At eleven seats with an eight-signature threshold, three signers may be unavailable before a veto or emergency execution is stalled. This is one more than the current Protocol Council tolerates on its emergency route, and is the primary reason for selecting this threshold. It is supported by a signer availability commitment (24 hours for emergency actions, 48 hours otherwise), by periodic unannounced signing drills, and by a liveness floor requiring membership replacement by Council Review and Approval if the Council falls below nine active signers.

**Pause as the fast path.** The Polygon Labs Operations multisig can pause at 5/9 without Council involvement, so incident containment does not depend on assembling eight signatures. Only unpausing requires the Council, which bounds the damage a compromised Operations multisig can do to denial of service rather than loss of funds.

**Pseudonymity.** Not publishing individual identities reduces targeting risk and weakens external accountability at the individual level. Accountability is preserved at the organizational level, where reputational stake is concentrated and where the participating organizations are themselves security firms and protocol teams with public standing. Polygon Labs retains full internal records of signer identities and key custody arrangements.

**Concentration across councils.** Some participating organizations hold seats on other protocols' security councils. This is normal for a small pool of qualified operators and is not disqualifying, but it is a correlated failure point and should be tracked as membership evolves.

**Migration risk.** During Phase 1, contracts in scope will transition between signer sets at different times. The migration order will be published in advance and executed one Safe at a time, so the governing set of every contract is known throughout.

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
