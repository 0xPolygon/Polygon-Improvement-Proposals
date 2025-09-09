---
PIP: 57
Title: Add migrateTo() in the POL Migration Contract
Author: Simon Dosch
Description: Adds a migrateTo function in the POL migration contract
Discussion: https://forum.polygon.technology/t/pip-57-add-migrateto-in-the-pol-migration-contract/20582
status: Final
Type: Core
Date: 2025-01-16
---

## Abstract 
This proposal introduces a new ```migrateTo``` function in the POL Migration contract. This function allows users to migrate MATIC to POL while specifying a recipient address different from their own, enhancing functionality and improving flexibility and usability. The contract version will be incremented to ```1.2.0```.

## Motivation 
The POL Migration contract only supports migrating MATIC to POL for the sender’s address. The current implementation creates a pain point for users or platforms who require the ability to migrate tokens to other designated recipients.

## Specification
The following changes will be made to the ```PolygonMigration.sol``` contract:
New ```migrateTo``` Function:
```solidity
function migrateTo(address recipient, uint256 amount) external {
    emit Migrated(msg.sender, recipient, amount);

    matic.safeTransferFrom(msg.sender, address(this), amount);
    polygon.safeTransfer(recipient, amount);
}
```
Updated ```Migrated``` Event:

The ```Migrated``` event will now include a separate ```recipient``` address.

```solidity
event Migrated(address indexed account, address recipient, uint256 amount);
```
## Security Considerations 
The migrateTo function relies on safe transfer methods to mitigate risks associated with token transfers. Only users with sufficient MATIC balances and valid approvals can call migrateTo and the Migrated event ensures traceability for all migrations.

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).


