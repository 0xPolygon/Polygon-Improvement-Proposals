---
PIP: 77  
Title: Update Membership of the Protocol Council  
Authors: Christopher von Hessert 
Description: Proposal to update the membership of the Polygon Protocol Council  
Discussion:  
Status: 
Type: Contracts  
Date: 2026-01-01
---

## Abstract  
This proposal seeks to update the membership of the Protocol Council established by [PIP-29](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-29.md) to ensure continued operational efficiency and governance transparency. This proposal supersedes and should be read in conjunction with PIP-29.

## Motivation  
Refreshing the Protocol Council membership ensures alignment with evolving community representation, and maintains operational transparency and efficiency. 

Updates to Protocol Council membership at this time were motivated by [PIP-54](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-54.md) and [PIP-68](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-68.md), which jointly seek to improve the  efficiency and decentralization of Polygon PoS by granting more efficient control over contract upgradeability to the Protocol Council.  In preparing for this transition, all current members of the Council were asked to reconfirm their interest, alignment, and availability to serve on the Council. 

As a result of this process, the updated membership list reflects the removal of Gauntlet, Mariano Conti & zackXBT.  The removal of each member is proposed with their full consent and in no way is a reflection on their skills, abilities, or the quality of their contributions. Pioneered security reviews for innovative technologies, including zero-knowledge proofs.

The below individuals are furthermore proposed to fill the seats left vacant by the above members: 

**Ryan Wegner**  


**Vahe Karapetyan**  
Vahe Karapteyan is a cybersecurity veteran with 15+ years of experience spanning low-level system security, offensive research, and blockchain internals, and is the Co-Founder & CTO of Hexens. He is a multi-time international CTF winner known for pioneering audits in zero-knowledge technologies and for building scalable vulnerability research tools for smart contracts.

**Sameep Singhania**


## Specification  
This PIP proposes the reform of the signer composition of the Protocol Council, to be updated to the list below. The existing multisig contract specification, including signature policies and timelock delays, will remain unchanged. 

**Updated Protocol Council Members**

| Name                | Affiliation                | Address                                      |
|---------------------|----------------------------|----------------------------------------------|
| L2 Beat             | —                          | 0xaE8B85DcaBb12EB2dDb11dAd1ed968b7eD81B410   |
| Mehdi Zerouali      | Sigma Prime                | 0x6d52F5F1A46304Ee51dd63D33cf1A7Be67EB9250   |
| Vahe Karapetyan     | Hexens                     | 0x21887c89368bf918346c62460e0c339113801C28   |
| Ryan Wegner         | Sentient Labs              | 0xda66df3920091ef4b54782b9463587c314dadd41   |
| Sanmeep Singhania   | Quickswap & Kalqix         | 0xb771380f912E4b5F6beDdf81314C383c13F16ab5   |
| Liz Steininger      | Least Authority            | 0x6860Ab2888f71AC09bEdEBB594b5B50299aC7889   |
| Viktor Bunin        | Coinbase                   | 0xBb9D37Ae9e63a4517bE5CE1D98eB9D89938fb651   |
| Jerome de Tychey    | ETH CC                     | 0x1aE033D45ce93bbB0dDBF71a0Da9de01FeFD8529   |
| Zaki Manian         | Sommelier Finance          | 0x096CA3674329bB66dD7CC14D1511dfB7728b9193   |
| Multisig Signer     | Polygon Labs (Engineering) | 0x4e981bae8e3cd06ca911fffe5504b2653ac1c38a   |
| Multisig Signer     | Polygon Labs (Security)    | 0x9d851f8b8751c5FbC09b9E74E6e68E9950949052   |
| Pablo Sabbatella    | Independent                | 0xAB4045C93e4eFFa9b325F706C9a690Ed00d08958   |
| Jack Sanford        | Sherlock                   | 0x342EBaca3ACC54d6f5Ee78073FeC4af07f42B94e   |

## Backward Compatibility  
This change is fully backward compatible, changing only the council membership. 

## Security Considerations  
Key security parameters remain unchanged. Considerations need to be made to ensure new signers are selected to maximize jurisdictional diversity, availability, and responsiveness.

## Copyright  
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
