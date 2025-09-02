---
PIP: 67  
Title: Update Membership of the Protocol Council  
Authors: Kaitlin Beegle, Harry Rook 
Description: Proposal to update the membership of the Polygon Protocol Council  
Discussion: https://forum.polygon.technology/t/pip-67-update-membership-of-the-protocol-council/21007 
Status: Final
Type: Contracts  
Date: 2025-04-15
---

## Abstract  
This proposal seeks to update the membership of the Protocol Council established by [PIP-29](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-29.md) to ensure continued operational efficiency and governance transparency. This proposal supersedes and should be read in conjunction with PIP-29.

## Motivation  
Refreshing the Protocol Council membership ensures alignment with evolving community representation, and maintains operational transparency and efficiency. 

Updates to Protocol Council membership at this time were motivated by [PIP-54](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-54.md) and [PIP-68](https://github.com/maticnetwork/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-68.md), which jointly seek to improve the  efficiency and decentralization of Polygon PoS by granting more efficient control over contract upgradeability to the Protocol Council.  In preparing for this transition, all current members of the Council were asked to reconfirm their interest, alignment, and availability to serve on the Council. 

As a result of this process, the updated membership list reflects the removal of Justin Drake (Ethereum Foundation) and Anthony Sassano (Daily Gwei).  The removal of each member is proposed with their full consent and in no way is a reflection on their skills, abilities, or the quality of their contributions. 

The below individuals are furthermore proposed to fill the seats left vacant by Justin and Anthony’s departure:

**Pablo Sabbatella**  
Pablo Sabbatella, also known as pablito.eth, is a blockchain operational security researcher. He is the founder of Opsek, which offers operational security audits and training to web3 companies.  He is also a member of SEAL (Security Alliance) and the Optimism Security Council.  He is host of the Blockchain Security Series podcast. 

**Jack Sanford**  
Jack is the CEO and Co-Founder of Sherlock.  Over the last 5 years, Jack has worked closely with hundreds of crypto teams to deliver successful security outcomes, including many of the biggest DAOs in the space.  Jack has hands-on experience in high stakes situations including war rooms, exploit mitigation, and funds recovery.  Jack has been a leading voice in the crypto security ecosystem, speaking about security topics at DevCon, DeFi Security Summit, ETHDenver, and more. 

Each of the above members have been audited and approved to join by existing members of the Council. 

The updated membership list also proposes replacing two (2) individual representatives from Polygon Labs Engineering and Polygon Labs Security teams- Mudit Gupta, and Jordi Baylina-  with two (2) ⅗ multisigs, one held by each organization. 

* Polygon Labs Engineering will propose upgrade payloads while also acting in a traditional signer capacity.   
    
* Polygon Labs Security will ensure operational integrity throughout the upgrade process by verifying payloads through to execution.

## Specification  
This PIP proposes the reform of the signer composition of the Protocol Council, to be updated to the list below. The existing multisig contract specification, including signature policies and timelock delays, will remain unchanged. 

**Updated Protocol Council Members**

| Name                | Affiliation                | Address                                      |
|---------------------|----------------------------|----------------------------------------------|
| L2 Beat             | —                          | 0xaE8B85DcaBb12EB2dDb11dAd1ed968b7eD81B410   |
| Mehdi Zerouali      | Sigma Prime                | 0x6d52F5F1A46304Ee51dd63D33cf1A7Be67EB9250   |
| Mariano Conti       | Independent                | 0x703728858Eea4994169f8177caB4F6dBA9783EAA   |
| Gauntlet            | —                          | 0x683a4F9915D6216f73d6Df50151725036bD26C02   |
| zackXBT             | Independent                | 0xDd92aB9A4D6C9793969b3A10A11FC934D5d93a49   |
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
