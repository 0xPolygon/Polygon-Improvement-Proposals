# PIP-82: Agentic Commerce Gas Program
## Authors: David Silverman (@oneski22), John Egan, Akshat Gada, James Lawton
## Type: Core
## Table of Contents:
- Abstract
- Definitions 
- Motivation
- Specification
- Backward Compatibility
## Abstract
Polygon has become one of the leading chains in Agentic Commerce, attracting 20.3% of x402 transactions and 10.4% of total volume from the beginning of this year . This proposal calls for Polygon PoS to invest in this growing segment by contributing up to $1M worth of gas base fees spent by Agentic Commerce transactions, attracting continued investment and attention from builders and agents alike to continue choosing Polygon as their home for payments.

To accomplish this, the EIP-1559 burn recipient will be changed from 0x7A8ed27F4C30512326878652d20fC85727401854 to 0x3ef57def668054dd750bd260526105c4eeef104f. The existing PIP-65 fee distribution system will be used to periodically recycle eligible base fees, while sending the non-recycled POL to the current burn collector address (0x7A8ed27F4C30512326878652d20fC85727401854). Initially the recycle will be applied to public Polygon x402 facilitators (initial list of addresses included below).

This program will operate until the $1M is fully recycled or December 31, 2026 whichever comes first. This program may also be edited or terminated by this PIP being superseded by another PIP approved by Polygon Governance. POL will be valued at prevailing market rate at time of the recycling transaction and that amount will be subtracted from the $1M allowance.

## Definitions 

EIP-1559: EIP-1559 is a transaction pricing mechanism that includes a fixed-per-block network fee that is burned and dynamically expands/contracts block sizes to deal with transient congestion.

x402: x402 unlocks the next era of agentic commerce by turning the web into a programmable payments layer. By activating the long-dormant HTTP 402 status code, it enables AI agents and software to seamlessly transact on-chain for real-time access to data, services, and APIs. The result is a frictionless, machine-native economy where agents can discover, pay, and execute autonomously..

Agentic Commerce: Agentic commerce is a model where AI agents autonomously discover, evaluate, and purchase goods or services on a user’s behalf. Instead of humans manually browsing and checking out, software agents execute transactions based on goals, constraints, and real-time data. It enables a machine-to-machine economy where decisions and payments happen programmatically.

PIP-65 Fee Distribution System: A process by which Polygon Labs and Regen Financial distribute protocol funds according to formulas passed as PIPs by the Polygon Protocol Governance Process. To date it covers the EIP-1559 Priority Fee System for Polygon PoS and has processed over 7MM POL  in value.

## Motivation
The motivation for reducing gas fees on Agentic Commerce and x402 transactions is straightforward: agentic commerce cannot scale in the presence of per-transaction friction. AI agents executing high-frequency, low-value payments require predictable, near-costless execution to function efficiently. Even minimal gas fees introduce pricing distortion, complexity in agent decision logic, and economic drag that undermines the viability of machine-native micropayments.
By effectively reducing gas/operating costs for x402 flows, Polygon can intentionally position itself as the preferred settlement layer for agent-to-agent commerce. This change would attract builders developing autonomous systems, increase on-chain transaction volume, and strengthen network effects around programmable payments. If we want Polygon to lead in the emerging agent economy, we must remove the structural barriers that prevent it from operating at machine speed and scale.
Specification
Change the recipient of EIP-1559 Burn by adding a new entry in burntContract with the hardfork block number and the new address in Bor’s Genesis file to 0x3ef57def668054dd750bd260526105c4eeef104f.
## Backward Compatibility
This change is not backward compatible and will require a hard fork of Bor to redirect burned MATIC. The old EIP-1559 contract will still function and will continue receiving non-recycled POL, per the PIP.
## Copyright
All copyrights and related rights in this work are waived under CC0 1.0 Universal.


### Current Public Facilitator Addresses (will be updated as new facilitators are added)
Polygon Public Facilitators:
- 0x29df60c005506AA325d7179F6e09eB4b4875dAde
- 0xF09A94831C18566781f70937f0996B96EfE691C8
- 0x42618f623Ec19beFf78dE9DbBFB653BfEaC05D09
- 0x3202643514D128FF0B4625D2682c0244CF58131c
- 0x11DA3fe5ADA6f5382Ebe972f14C3585DA4E65AeA
- 0x135DfE729F9bbd7F88181E1B708d7506fd499140
- 0xDcb0Ac359025dC0DB1e22e6d33F404e5c92A1564
- 0x99EFc08BB42282716fB59D221792f5207f714C9d
- 0xbE5115800247405f020197BF473eBFd085a2C635
- 0x5eAb3D78264Dab340340d6a37Ff0836464Ae5773
- 0xE5D4197eFd5D03E3f30cBf11C0fF63Eb95a0A656
- 0xfac8Edb989f1ba7F9dBb7A1233542D4e1fD6144F
- 0xaFdbfaCb5ed691bf0bCFA660901f299ce9775489
- 0x1e48Ed59a502D0B324CdAf83362865b3ff49ABa2
- 0xA1dcBDC2C34577ACD4A1152A98807B2f281A112e
- 0x9e281D4e26E1a4e7C27014E2ca8Cee7F2D44fa52
- 0x76FCb8ae3365A487E6EA235386C1cf3AbADeDA60
- 0x9523B120C75640469f1D16490Da0388928229452
- 0x153F3A70e4400c211d9B482b62aD721Bb02F96F6
- 0xd5dD012019C58882Dd507A8b3fCBB7b62e9a24c3
- 0xfff23108338C218F895d75980E14688218D4E92a
- 0xF744e153Ef63f7EEe4a58e0F13761D16C2125EE3
- 0x0a8B10FE8Bd3072351600Adef4796F3F7aF72Ab0
- 0x971b4079A618F72Fa0F1792b07ed5923dfBF3500
