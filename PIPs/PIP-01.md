---
PIP: 1
Title: PIP Purpose and Guidelines
Description: Describes community standards for the Polygon ecosystem, including core protocol specifications
Author: Harry Rook (@hrook1), Mateusz Rzeszowski
Discussion: https://forum.polygon.technology/t/pip-1-pip-purpose-and-guidelines/11356
Status: Continuous
Type: Informational
Date: 2023-02-22
---

## Abstract 

Polygon Improvement Proposals (PIPs) describe community standards for the Polygon ecosystem, including core protocol specifications such as Heimdall and Bor, client APIs, and contract standards, etc.

### What is a PIP?

PIP stands for Polygon Improvement Proposal. A PIP is a design document providing information to the Polygon community, or describing a new feature for Polygon or its processes or environment. 

PIPs are used by authors to measure, document, and build community consensus, while providing technical specifications and motivation behind a specific feature proposal. 

## PIP Rationale

In a decentralized and self-governed system, a framework is necessary for the developer community to inform, propose, and gather technical feedback on new features or changes to existing features. 

By introducing a transparent and versioned repository, the community maintains a historical record of all feature proposals, including their revision history and implementation progress.

The main discussion space for all PIPs is the [Polygon Community Forum](https://forum.polygon.technology). Feedback from the forum will be incorporated into the documented PIPs housed in the Github repository. 

### PIP Principles

In order to ensure PIPs can serve their intended purpose, a PIP author should check that the PIP content is consistent with the principles below: 

- Uniqueness: PIP authors should make sure their proposal is the first. Should there be an existing proposal already, authors should contact the previous author to modify/extend the proposal.

- Understandability: PIP content should be understandable without risking oversimplification. A PIP author should aim to ensure there’s only one possible interpretation of proposed changes. 

- Conciseness: PIP content must be as short as possible while maintaining its focus.

- Precision: PIP content must precisely reflect a narrowly-defined subject. Where possible, complex subjects should be split into complementary PIPs. 

- Thoroughness: PIP content must address or reference all relevant aspects with regard to implementation and existing PIPs, among others. 

### PIP Types

The following are the general categories for PIPs:

- **Core:** Improvements to the core components of the network, such as Heimdall and Bor, as well as changes that are not necessarily consensus critical but may be relevant to core components.

- **Contracts:** Improvements on core L1 contracts that are deployed on Ethereum.

- **Interface:** Improvements around client API/RPC specifications and standards, and also certain language-level standards like method names and contract ABIs. An interface PIP doesn’t require on-chain consensus, but may be adopted by the wider ecosystem, e.g., a new token standard.

- **Informational:** Recommendations, information, or general thinking on an issue presented to the community. 

## PIP Flow In All Tracks

### Idea

1. Initial post is created on the Polygon community forum.
2. In addition to making sure your idea is original, it will be your role as the author to make your idea clear to reviewers and interested parties, as well as inviting editors, developers and community to give feedback on the aforementioned channels.

### Draft

3. A PIP should then be assembled using the template  suggested in ‘PIP Body’ below. PIP content should adhere to the PIP principles mentioned previously. Should the document require amendments, the PIP editors will work with the author to amend any changes required. The author is then responsible for amending the document and resubmitting the request. Once all issues are resolved, it will be uploaded to the Polygon Improvement Proposal Github repository. 

### Review

4. The author then marks the PIP as ready for and requesting Peer Review. The proposal can then be discussed in a ‘Polygon Builders Session’, as well as broadcasted to the Discord Announcement channels to let the community know about the latest PIP.

### Last Call 

5. In the final review window for a PIP before moving to ‘Final’, a PIP Editor will assign ‘Last Call’  status to the proposal. 
6. If this period results in necessary normative changes, the PIP will revert back to Peer Review.

### Final 

Once the author has addressed all of the feedback and the proposal content has been refined and finalized, the PIP will then be translated to an issue on GitHub on their respective repositories. This represents the final standard of a PIP and should only be updated to correct errata and add non-normative clarifications.
Only once the implementation has gone live on mainnet, will the PIP receive the ‘final’ status. 

The Polygon Labs development team, or any other development team, can then prioritize and implement the PIP. This will then be tested on the Mumbai Testnet and then rolled out as an upgrade on the Mainnet.


## PIP Body 

PIPs should contain the following:

**Title:** The Title of the PIP should  be concise and accurately explain the purpose of the PIP

**Status:** 1 of 5 statuses for the PIP

Draft: When a new PIP is submitted, the status will remain Draft during the discussion stages. Authors need to ensure that they add this prefix to their Title. During this stage amendments to the Draft will be subject to discussions.

Review: Once the author decides the proposal outline has taken shape, the author then marks the proposal as requesting ‘Peer Review’. 

Last Call: Following completion of the Peer Review, a final review window starts for a PIP before moving to Final. A PIP editor will assign Last Call status and set a review end date. This will only happen when the author perceives there is a consensus on the PIP reaching a finalized state. 

Final: This state represents the Final standard. No more amendments should be made to the PIP at this stage. 

Continuous: A special status for PIPs that are designed to be continually updated and not reach a state of finality. This includes, most notably, PIP-1.

Withdrawn: The PIP author(s) have withdrawn the proposed PIP. This state is final and once a proposal is marked as withdrawn, it can no longer be resurrected using the PIP number. If the idea becomes relevant at a later date, it may be considered under a new proposal. 

**Abstract:** A short (~200 word) description of the technical issue being addressed.

**Motivation:** Motivation is critical for PIPs that want to change the Polygon protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the PIP solves and inform the community of any potential self-interest the author has in making the proposed change to the Polygon protocol. PIP submissions without sufficient motivation may be rejected outright by editors.

**Specification:** The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Polygon protocols

**Rationale:** The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g., how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.

**Backwards Compatibility:** All PIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The PIP must explain how the author proposes to deal with these incompatibilities. PIP submissions without a sufficient backwards compatibility treatise may be rejected outright by the community.

**Test Cases:** Test cases for implementation are mandatory for PIPs that are affecting consensus changes. Other PIPs can choose to include links to test cases if applicable.

**Reference Implementation:** An optional section that contains a reference/example implementation that people can use to assist in understanding or implementing this specification.

**Security Considerations:** All PIPs should contain a section that discusses the security implications/considerations relevant to the proposed change. A PIP should include information that might be important for security discussions, surface risks, and be usable throughout the life cycle of the proposal. For example, an author may include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks, and how they are being addressed. PIP submissions missing the “Security Considerations” section may be rejected by the community. A PIP cannot proceed to status “Final” without a Security Considerations discussion deemed sufficient by community reviewers.

**Copyright:** All PIPs must provide a license compatible with the original code it modifies or make available and waive all copyrights and related right under CC0 1.0 Universal.

## PIP Editor Responsibilities

For each new PIP that comes in, an editor does the following:

Read the PIP to check if it is ready, sound and complete. The ideas must make technical sense, even if it appears unlikely that the PIP will  get to Final status.
Ensures the title accurately describes the content.
Check the PIP for language (spelling, grammar, sentence structure, etc.).
If the PIP isn’t ready, the editor will send it back to the author for revision, with specific instructions.
Once the PIP has satisfied the above requirements, a PIP editor will upload the proposal to the GitHub repository. 

### PIP Editors

The current PIP editors are comprised of:

- [Parvez Shaikh](https://github.com/MaticParvez)


## References

The PIP framework, and in particular PIP-1, was heavily derived from the [Ethereum EIP-1](https://eips.ethereum.org/EIPS/eip-1) document [Martin Becze, Hudson Jameson, et al., "EIP-1: EIP Purpose and Guidelines," Ethereum Improvement Proposals, no. 1, October 2015. [Online serial] Available: https://eips.ethereum.org/EIPS/eip-1.], which was derived from [Bitcoin’s BIP-0001](https://github.com/bitcoin/bips) (written by Amir Taaki) which in turn was derived from [Python’s PEP-0001](https://peps.python.org/) (written by Barry Warsaw, Jeremy Hylton, David Goodger, and Nick Coghlan)

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
