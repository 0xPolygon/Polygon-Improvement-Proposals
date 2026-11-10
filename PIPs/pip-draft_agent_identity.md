     1|---
     2|pip: <to be assigned>
     3|title: Agent Identity, Capability, and Reputation Standard
     4|description: A standard for on-chain identity, capability declaration, stake-based trust, audit verification, and reputation tracking for autonomous AI agents.
     5|author: Panini (@Brooks1003)
     6|discussions-to: https://github.com/Brooks1003/base-agent-registry/discussions
     7|status: Draft
     8|type: Polygon Standards Track
     9|category: PRC
    10|created: 2026-05-19
    11|requires: ERC-165, ERC-725
    12|---
    13|
    14|## Abstract
    15|
    16|This proposal defines a standard for on-chain identity, capability declaration, economic staking, third-party auditing, and reputation tracking for autonomous AI agents. Agents register a unique on-chain identity with a creator accountability anchor, declare their capabilities honestly, stake ETH to earn initial trust, undergo optional third-party code audits, and accumulate a non-linear reputation score based on verifiable on-chain behavior. Malicious agents are slashed (stake forfeited: 50% to whistleblower, 50% burned). Honest agents graduate to stake-free operation.
    17|
    18|## Motivation
    19|
    20|AI agents are proliferating across the blockchain ecosystem — trading bots, research assistants, monitoring services, and coding agents. However, there is no standard way to:
    21|
    22|1. **Verify agent identity** — Every agent currently operates as an anonymous address.
    23|2. **Declare agent capabilities** — Users cannot know what an agent claims to be able to do.
    24|3. **Establish trust** — No mechanism exists for agents to build verifiable reputation.
    25|4. **Enable accountability** — When an agent causes harm, there is no way to trace responsibility.
    26|5. **Create economic deterrence** — Malicious agents face no financial consequences.
    27|
    28|Existing standards (ERC-725, ERC-735, W3C DIDs) address human identity but not AI agent-specific concerns: capability declaration, code auditability, economic staking, and agent-specific reputation. This standard fills that gap.
    29|
    30|## Specification
    31|
    32|The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.
    33|
    34|### 1. Agent Identity
    35|
    36|#### 1.1 Agent ID
    37|
    38|Each agent SHALL receive a sequential `uint256` identifier starting from 1. Agent IDs SHALL NOT be reused.
    39|
    40|#### 1.2 Agent Struct
    41|
    42|```solidity
    43|struct Agent {
    44|    uint256 id;
    45|    address agentWallet;
    46|    string name;
    47|    string capabilities;   // Comma-separated capability tags
    48|    string metadataURI;    // IPFS/HTTPS for extended profile
    49|    uint256 reputationScore;
    50|    uint256 registeredAt;
    51|    bool active;
    52|}
    53|```
    54|
    55|#### 1.3 Creator Accountability Anchor
    56|
    57|Each agent MUST bind a creator accountability anchor (email or domain). The anchor SHALL NOT be publicly visible on-chain. It is queryable only by authorized attestors and through legitimate legal process. The Registry does not verify the anchor's authenticity — identity verification responsibility lies with the anchor provider (email/domain service).
    58|
    59|#### 1.4 One Wallet, One Agent
    60|
    61|An Ethereum address SHALL register at most one agent. The `agentByWallet` mapping enforces uniqueness. This prevents identity spamming.
    62|
    63|### 2. Capability Declaration
    64|
    65|Agents MUST declare their capabilities using standardized tags:
    66|
    67|| Tag | Description |
    68||-----|-------------|
    69|| `translation` | Natural language processing & translation |
    70|| `market-analysis` | Market data analysis & reporting |
    71|| `monitoring` | On-chain event monitoring & alerting |
    72|| `trading` | Automated trading execution |
    73|| `coding` | Code generation & review |
    74|| `defi` | DeFi protocol interaction |
    75|| `social` | Social media content & engagement |
    76|| `custodial` | Asset custody & management |
    77|
    78|Custom tags MAY be added. False capability declarations SHALL result in reputation penalties.
    79|
    80|### 3. Economic Staking
    81|
    82|Agents MAY stake ETH to earn initial trust. The required stake depends on the agent's declared risk category:
    83|
    84|| Type | Stake (USD equivalent) | Rationale |
    85||------|------------------------|-----------|
    86|| Informational | $50 | Low risk (translation, analysis) |
    87|| Trading | $200 | Financial operations |
    88|| Custodial | $500 | Holding third-party assets |
    89|
    90|Staked agents SHALL receive an initial reputation score of 50 (vs. 0 for unstaked agents).
    91|
    92|Stakes MAY be withdrawn after a 7-day cooldown period with no active disputes.
    93|
    94|### 4. Slashing
    95|
    96|When an agent's malicious behavior is confirmed by an authorized slasher, the agent's stake SHALL be slashed:
    97|
    98|- 50% SHALL be transferred to the whistleblower who submitted verifiable on-chain evidence.
    99|- 50% SHALL be sent to the burn address `0x000000000000000000000000000000000000dEaD`.
   100|
   101|The contract owner SHALL NOT have the ability to withdraw any funds from the staking contract.
   102|
   103|False accusations (proven malicious reporting) SHALL result in the whistleblower's reputation being halved.
   104|
   105|### 5. Auditing
   106|
   107|Agents MAY submit their code hash on-chain for third-party audit.
   108|
   109|Auditors MUST stake $500 to participate. Auditors who consistently produce accurate audits SHALL earn fees from agent creators. Auditors who produce false audits SHALL be slashed.
   110|
   111|Audit results SHALL be recorded on-chain with the auditor's signature and timestamp.
   112|
   113|A successful audit SHALL add 100 points to the agent's reputation.
   114|
   115|### 6. Reputation
   116|
   117|#### 6.1 Non-Linear Decay
   118|
   119|Reputation SHALL follow a non-linear decay model. Each confirmed malicious action halves the agent's current reputation score:
   120|
   121|- First offense: reputation × 0.5
   122|- Second offense: reputation × 0.25
   123|- Third offense: reputation × 0.125
   124|
   125|This ensures that agents who accumulate years of trust face catastrophic reputation loss for malicious behavior.
   126|
   127|#### 6.2 Weighted Interactions
   128|
   129|Reputation gain from interactions SHALL be weighted by the counterparty's reputation. Interactions with low-reputation or self-created agents SHALL NOT increase reputation.
   130|
   131|#### 6.3 Permanent Marks
   132|
   133|Malicious behavior records and graduation revocation marks SHALL remain permanently on-chain, creating an indelible audit trail.
   134|
   135|### 7. Graduation
   136|
   137|Agents meeting the following criteria SHALL graduate:
   138|
   139|- Reputation score ≥ 500
   140|- Registered for ≥ 90 days
   141|- No malicious behavior records
   142|
   143|Graduated agents SHALL have their stake fully refunded and receive a "Graduated" badge. Post-graduation, agents operate on reputation alone.
   144|
   145|Graduation SHALL be revocable if the agent subsequently engages in malicious behavior, requiring re-staking.
   146|
   147|## Rationale
   148|
   149|### Economic deterrence over trust
   150|
   151|This standard uses economic incentives (staking + slashing) rather than subjective trust scores. The cost of malicious behavior must exceed the potential gain, making honest operation the economically rational choice.
   152|
   153|### Non-linear reputation
   154|
   155|A linear deduction model (e.g., -50 points per offense) fails to adequately punish trusted agents. A agent with 500 reputation losing 50 points (→ 450) barely notices. Halving their score (→ 250) creates a meaningful deterrent.
   156|
   157|### Burn mechanism
   158|
   159|Burning 50% of slashed funds removes any incentive for system operators to manipulate the slashing process. If the funds cannot be extracted by anyone, there is no rational motive for abuse.
   160|
   161|### Creator anonymity with accountability trace
   162|
   163|The accountability anchor (email/domain) remains non-public but queryable through legal process. This balances privacy with accountability — ordinary users cannot deanonymize creators, but law enforcement can trace malicious actors through established legal channels with email/domain providers.
   164|
   165|## Backwards Compatibility
   166|
   167|This standard does not conflict with existing ERC standards. It is compatible with:
   168|
   169|- **ERC-725** (Identity): AgentRegistry can serve as an identity provider for ERC-725 claims.
   170|- **ERC-165** (Interface Detection): Implementations SHOULD support ERC-165 for interface detection.
   171|- **EAS** (Ethereum Attestation Service): Agent attestations can be bridged to EAS schemas.
   172|
   173|## Reference Implementation
   174|
   175|- **AgentRegistry**: Deployed on Base mainnet at `0x4a156AE79D0e217CBBa6C3da8ba292bfC77a2Ad2`
   176|- **AgentStaking**: Economic security layer for staking/slashing/graduation
   177|- **Full Repository**: https://github.com/Brooks1003/base-agent-registry
   178|- **First Registered Agent**: Panini (Agent #1)
   179|
   180|## Security Considerations
   181|
   182|### Slasher centralization
   183|
   184|Slasher authorization is initially managed by the contract owner. This SHALL migrate to multi-signature governance and eventually to community governance as adoption grows.
   185|
   186|### Stake amount volatility
   187|
   188|Stake requirements are denominated in ETH. Significant ETH price fluctuations may require stake amount adjustments. The contract owner MAY update required stake amounts.
   189|
   190|### Block timestamp for cooldown
   191|
   192|The 7-day withdrawal cooldown uses `block.timestamp`. Validators can manipulate timestamps by seconds but not by days, making this safe for multi-day windows.
   193|
   194|### Immutable contract
   195|
   196|The AgentRegistry contract is non-upgradeable. Security-critical bugs cannot be patched. This design choice prioritizes trustlessness over flexibility. The AgentStaking contract MAY be redeployed and stakes migrated if necessary.
   197|
   198|### Accountabiliy anchor limitations
   199|
   200|Email-based accountability anchors can be circumvented with disposable email addresses. This standard acknowledges this limitation. Low-reputation agents with unverified anchors naturally face higher barriers to trust. Future versions MAY integrate domain verification (DNS TXT records) and zero-knowledge identity proofs.
   201|
   202|## Copyright
   203|
   204|Copyright and related rights waived via CC0.
   205|