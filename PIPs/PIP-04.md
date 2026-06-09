---
PIP: 4
Title: Validator Performance Management Proposal
Description: >-
  Proposes a framework that will allow further decentralization of the network
  by means of validator self-regulation.
Author: 'Eric Hill, Harry Rook (@hrook1), Mateusz Rzesowski'
Discussion: 'https://forum.polygon.technology/t/pip-4-validator-performance-management/9956'
Status: Stagnant
Type: Contracts
Date: 2022-08-22T00:00:00.000Z
---

## Abstract

In its current state, the PoS validator network is not entirely permissionless in that the previously-selected set of validators has largely persisted since testnet. 

Following the spirit of gradual decentralization, certain steps need to be taken with the end state being the community assuming care for the network.

## Motivation 

To arrive at a state of absolute decentralized self-governance, as was mentioned back in the [mainnet announcement](https://blog.polygon.technology/mainnet-is-going-live-announcing-the-launch-sequence-matic-network/), the validators will have to self-regulate network participation to an agreed set of parameters.

Following on from the considerations and feedback of validators on the forum and elsewhere, the Polygon Governance Team wants to now put forward the following proposal for validators to accept or reject.

## Rationale

Self-regulation in this context refers to setting and administering conditions for the admission, participation, and if necessary, forced exit of validators from the active set - with the last part formalizing previously-achieved consensus on the subject.

Successfully engaging in self-regulation requires setting parameters with fair, transparent, and self-enforcing standards for:

1. Measuring performance;
2. Compliance with conditions of participation;
3. Choosing and acting on remedial measures; and
4. Addressing compliance breaches if and when they arise.

Under the proposed parameters a validator who underperforms the benchmarks for 700 checkpoints will have a second 700 checkpoint period to correct the deficiency before a process to unbound its stake is implemented.

The parameters in Part A and Part B were chosen following feedback from the validator community specifically and the Polygon community at large. 

If adopted, the framework would allow further decentralization of the network by means of validator self-regulation. 

As a result of the increased amount of validator churn that may occur as a result of Part A and B, Part C details the roadmap and process for which new validators would be admitted into the set in a way that promotes network health and aligns with the notion of gradual decentralization.

In addition to the above, Part D proposes to increase the validator set from 100 to 105. This would increase the economic security and decentralization of the network. 

## Specification

### **Part A**

Here we propose a potential framework to manage validator performance through a self-enforced performance standard across the network.

### **1. Proposed Potential Parameters**

Measure individual validator performance based upon checkpoints signed over a specified time period, performed on a rolling basis at each new checkpoint. Then measure this against a benchmark of the total network performance in that same time period, as detailed below:

> * Monitoring Period (“MP") = previous 700 checkpoints, updated every new checkpoint.
> * Take % of checkpoints signed by each validator in the MP, combine all % values and find the median average. 
> * Multiply median average by an agreed multiple = Performance Benchmark (PB).
> * At each checkpoint, calculate the % of checkpoints signed in the MP by individual validators and measure against the PB.

### **2. Performance Benchmark**

In order to transition validators into the process requiring satisfaction of the proposed potential parameters, there will be a slightly lower benchmark for the first ~2 months, while validators become accustomed to the parameters. 

> * PB1 → 95% of median average of last 700 checkpoints signed by validator set (first 2,800 checkpoints)
> * PB2 → 98% of median average of last checkpoints signed by validator set (continues thereafter)

### **3. Validator Performance Dashboard**

If validators adopt this proposal, then a public dashboard would be developed to provide live monitoring of individual and overall validator performance. 

The dashboard would serve the functions listed below.

At launch:

* Provide real time visual representations of validator performance telemetry.
* Display validator notices and updates on their status, either meeting performance benchmarks or being deficient.

In a future release:

* Provide direct notifications to validators, alerting them to their deficient status.

#### **4. Notice to Validators**

If the validators adopt this proposal, after the MVP dashboard is developed and deployed, validators will be given 24 hours notice of the commencement of performance monitoring, with the data set beginning 24 hours after the notice is sent. 

### Part B

If the validators adopt this proposal, then any validator that falls below any performance benchmark would enter the process below:

#### **1. Remedial measures and corrective action**

Deficient Validator Process:

> * If validator <PB in the MP → Grace Period (“GP”).
> * If validator in GP <PB after 700 checkpoints → Notice of Deficiency (“NOD”), enters into Grace Period 2 (“GP2”).
> * If validator in GP2 <PB after 700 checkpoints → Final Notice (“FN”), validator will be unstaked with no further resource. 

**Definitions:**

The GP is 700 checkpoints long and allows a validator time to bring its performance back above the PB. If the deficiency is corrected within the GP, then there would be no further action.

Failure to become compliant in the GP would result in issuance of a public NOD from the Performance Monitor (“PM”). The operator would have a 700 checkpoint period to correct the deficiency in GP2. If the deficiency is corrected within the NOD period, then there would be no further action, however the NOD would remain public.

Failure to become compliant in the GP2 would result in issuance of a FN of the intent of the community to implement a forced exit procedure by offboarding the validator from the network by unbonding their stake.

### **2. Forced Unstaking**

The unstaking of the deficient validator would be done as follows:

Call `ForceUnstake` function in the `stakeManager` contract via the 0xFa7D2a996aC6350f4b56C043112Da0366a59b74c multisignature wallet.

### **Part C**

#### **1. Validator Admission**

To repopulate the validator slots that may become vacant as a result of the forced unstaking detailed in Part B, below is the proposed roadmap for the admission and onboarding of new validators into the set:

* Phase 1 (current) - The multi-sig holders would set the admission parameters and choose the validators.
* Phase 2 (next step) - The governance and validator communities set and approve the parameters, and the multi-sig holders implement those parameters.
* Phase 3 (end state) - The governance and validator communities set the parameters and admit and onboard validators themselves.

#### **2. Irregular Joining**

Joining the validator network consists of two steps: admission and onboarding. These steps and specific parameters relating to each will ultimately be controlled by the governance community. This community approach enhances network security with a level of certainty about the intent of the actors trying to join the network, and introduces social ramifications, which can ensure proper functioning of the network, e.g., validator onboarding and education, participation in off-chain and on-chain governance, efficient communication on critical updates and/or node performance issues.

Irregular joining occurs when a new validator joins the network, but does not follow or circumvents the community-approved admissions and onboarding process. Bot sniping of a node is an example of an instance of irregular joining.

As irregularly-joining validators do not participate in the onboarding process, they cause massive issues for the network health. In particular, the inability to communicate on critical updates and/or node performance issues is problematic.

As a result, we propose if this proposal is adopted:

1. Participation in the admission process (outlined in Part C) will be viewed as obligatory;
2. Any future participants who circumvent the process will be unstaked from the network;
3. The minimum $MATIC staked will be increased from 1 to 10,000 in order to deter snipers. 

### **Part D**

#### **1. Increasing the Validator Set**

At times, a situation may arise in which an underperforming validator has already been offboarded, following a Final Notice, but a new one has not yet been onboarded to the network. This essentially means that the active validator count may occasionally drop below 100. Additionally, validators that underperform for prolonged periods of time effectively reduce the active set. 

In order to maintain network integrity and guarantee an active set of 100 efficient validators, we propose increasing the maximum validator count to 105 from 100.

As the network has matured and seen wide adoption, it seems warranted to provide new validator slots for added decentralization and security. A conservative increase of 5 slots seems like it would serve as a good starting point.

## Backward Compatibility 

This proposal creates no backward compatibility issues.

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
