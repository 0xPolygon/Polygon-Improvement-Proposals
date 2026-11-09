---
PIP: 59
Title: Moving the Floor Gas Price of 25 Gwei from Priority Fee to Base Fee
Author: Manav Darji, Sandeep Sreenath
Description: Proposes transitioning the minimum gas price of 25 Gwei from the priority fee to the base fee in the Polygon PoS network.
Discussion: https://forum.polygon.technology/t/pip-59-moving-the-floor-gas-price-of-25-gwei-from-priority-fee-to-base-fee/20609
status: Final
Type: Core
Date: 2025-02-11
---

## Abstract 
This proposal seeks to move the current 25 Gwei minimum gas price from the priority fee to the base fee. By integrating the floor gas price into the protocol-level base fee, wallets and dApps can more easily query and adapt to changes, reducing manual configurations and improving user experience.

## Motivation 
The 25 Gwei minimum gas price is currently enforced as a priority fee, configured at the client level. This creates challenges for wallets and dApps that cannot programmatically query the priority fee floor since it is not part of the protocol-level base fee.

When changes are made to the minGas priority fee, wallet providers and other downstream applications must be manually updated, increasing the risk of inconsistencies.

Without knowledge of the correct priority fee floor, users may experience failed transactions (when the floor price is increased) or overpay fees (when the floor price is decreased), degrading UX.

By moving this configuration to the base fee, the floor gas price becomes part of the protocol, ensuring easier access for all applications and reducing the need for manual intervention.

## Specification

### Base Fee Calculation
This includes modifying the base fee calculation to include a minimum enforced value of 25 Gwei.

When the next block's base fee is calculated, the protocol will enforce a 25 Gwei minimum price, overriding the EIP-1559 logic; if the calculated base fee is below 25 Gwei, it will be replaced by the 25 Gwei floor value.

```go
// Case when base fee is below gas target
minBaseFee = params.GetMinBaseFee(config.Bor, parent.Number)

// Enforce the minimum base fee for Polygon PoS
if config.Bor != nil {
    baseFee = math.BigMax(baseFee, minBaseFee)
}

```

A new protocol parameter will be added:
```go
MinBaseFee = 25000000000 // Minimum enforced base fee for EIP-1559 blocks (Polygon-specific).
```

## Backwards Compatibility
This change will not be backward compatible on the protocol consensus layer and will therefore require a hardfork. On the infrastructure layer, this change is backward compatible with existing wallets and apps that rely on querying the base fee.

Adequate notice should be provided on the deprecation of the minGas priority fee to RPCs, dapps, and wallets. Validators can still choose a higher minimum priority fee, which is configurable at the client level. 

## Security Considerations
Currently, the base fee is burned via the protocol, and the priority fee goes directly to the block producer as a reward. Moving the 25 Gwei enforcement from the priority fee to the base fee will mean transactions no longer require the minimum priority fee.

This could potentially reduce validator rewards in situations where the priority fee is ​​≤ 25 Gwei. Ultimately, the actual effect on validator-specific rewards will depend on user behavior and market dynamics. The impact of this change should be monitored in case of a material reduction in validator rewards.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
