---
PIP: 38
Title: Phased expansion of Validator Slots
Description: Proposes increasing validator slots to 125
Author: Vasanti Rode (@Vasanti01), Parvez Shaikh (@Maticparvez)
Discussion: https://forum.polygon.technology/t/pip-38-phased-expansion-of-validator-slots-on-polygon-pos/14200
Status: Draft
Type: Contracts
Date: 2024-05-05
---

## Abstract

Expanding the number of validators within the Polygon PoS network is a crucial step toward enhancing decentralization. By increasing the number of validators, validation responsibilities are distributed more widely, which reduces the risk of centralization and strengthens the network's security and resilience. This proposal outlines the considerations for expanding the number of validator slots to ensure robust network performance.

## Motivation

The primary motivation for increasing the number of validator slots within the Polygon PoS network is to improve decentralization which can be observed via the following metrics:

- **Geographical Distribution of Nodes**: Expanding the number of validator slots allows for a wider geographical distribution of nodes. By having validators in diverse locations globally, the risk of localized disruptions is reduced, and the network's health is enhanced to prevent regional failures.
- **Decrease Stake Centralisation**: More validator slots means that voting power and block production can be more evenly distributed across a larger number of validators.
- **Improve Nakamoto Coefficient**: The Nakamoto Coefficient, which measures the minimum number of validators required to compromise a network, should improve with an increase in validator nodes. A higher coefficient indicates a more secure and decentralized network, as it becomes harder for any group to disrupt the network's consensus.

## Specification

The proposed expansion involves a phased increase in the number of validator slots, starting from the current 105 slots to 110, then to 115, further to 120 and finally to 125. This incremental approach, with ongoing community review and feedback, allows for careful monitoring and management of each expansion stage. The specific steps include:

1. Assessing the current infrastructure and ensuring it can support the additional validators.
2. Implementing the first phase by adding 5 validator slots and monitoring the network's performance and stability.
3. Gradually increasing the slots in subsequent phases (as noted above) based on performance metrics and network impact.
4. Conducting thorough monitoring at each stage to ensure seamless integration and functionality.
5. In the event that the expansion from 105 to 110 validators negatively impacts the network's health, the community may consider taking corrective action to offboard the last 5 validators who were onboarded.

## Rationale

Increasing the number of validator slots in phases offers several advantages:

- It helps the network grow gradually, lowering the risks that come with adding many new validators all at once.
- Each phase can be evaluated for performance, ensuring that any issues are identified and addressed promptly.
- It ensures that the network infrastructure and support mechanisms are adequately scaled to handle the increased load, thereby maintaining overall network health and performance.

## Backwards Compatibility

The expansion of validator slots is designed to be fully backwards compatible. Existing validators and network operations should not be affected by the increase in slots. The system architecture should ensure that the integration of new validators occurs smoothly, without disrupting ongoing operations or diminishing the performance of current validators.

## Test Cases

To ensure the success of the validator slot expansion, the following test cases will be conducted:

- _Performance Testing_: Evaluate the network's ability to handle the increased number of validators, focusing on transaction processing times, block generation, and overall output.
- _Stability Testing_: Monitor the network for any signs of instability or degradation in performance following the addition of new validators.

## Security Considerations

Security remains a top priority during the expansion process. Security measures will include:

- Enhanced monitoring and alerting systems to detect and respond to any abnormal activity.
- Suggested implementation of best practices for validator operations.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
