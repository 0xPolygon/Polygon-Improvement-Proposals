---
PIP: 15
Title: Adding support for EIP-4337 Bundled Transactions
Description: Proposes and Implements an additional API to support Bundled Transactions
Author: Pratik Patil (@pratikspatil024)
Discussion: https://forum.polygon.technology/t/pip-adding-support-for-eip-4337-bundled-transactions/12679
Status: Final
Type: Interface
Date: 2023-08-02
---

## Abstract 

The Ethereum researchers have put forth a proposal for a new RPC endpoint called `eth_sendRawTransactionConditional` (implemented as `bor_sendRawTransactionConditional`), which is designed to facilitate the bundled transactions introduced in [EIP-4337](https://eips.ethereum.org/EIPS/eip-4337). This proposal has been successfully implemented in [PR#945](https://github.com/maticnetwork/bor/pull/945) within the Bor client in the `bor` namespace. The plan is to incorporate this endpoint into Bor's Mumbai and Mainnet versions to provide support for bundled transactions.

## Motivation

EIP-4337 aims to enable account abstraction while maintaining decentralization and censorship resistance. It strives to uphold a level of decentralization comparable to the underlying chain's block production process. The proposal achieves this by dividing validation and execution into separate steps when bundling transactions for submission to an alternative mempool. However, this separation introduces a potential challenge for bundlers working on their L2 transactions.

The problem arises from the time delay between the bundlers' simulated transaction validation and the final inclusion of those transactions by sequencers. This delay poses a risk of reverting bundle submissions. The reason for this risk is the lag between the initial submission of a transaction and its ultimate inclusion, during which a smart contract account's storage can change and invalidate the transaction. When bundled transactions are reverted, it results in a loss of value for the bundler. Consequently, this discourages the operation of these services until the issue is effectively addressed.

To tackle this problem, Ethereum researchers introduced the `bor_sendRawTransactionConditional` endpoint. This enhanced endpoint extends the functionality of the standard `eth_sendRawTransaction` endpoint by incorporating additional options that enable users to define specific valid ranges for block height, timestamps, and knownAccounts.

In this context, knownAccounts refers to a map of accounts with expected storage that must be verified before executing the transaction. By utilizing this feature, sequencers can now reject transactions that fail to meet the specified conditions for inclusion during the initial validation stage. Consequently, this effectively mitigates the risk associated with potential changes in account storage between the validation and execution phases of the transaction.

The `bor_sendRawTransactionConditional` API is privileged and can only be used if the bundler is connected to the validator (block producer). The conditional transactions will not be broadcasted to the peers, so if the bundler sends the conditional transaction to the sentry node, it will not be executed. There is a bidirectional trust involved between the validator and the bundler. The validator trusts that the bundler is not going to spam him, and the bundler trusts that the validator will not front-run the transaction.

## Specification

The reference implementation in Bor adds support for the `bor_sendRawTransactionConditional` API. It also adds checks to validate the ranges for block height, timestamps, and knownAccounts. These checks are performed at two locations, one at the API level (when the transaction is sent to the Bor client), and other in the worker module (when the transaction gets picked up from the transaction pool for inclusion in the block). The number of the slots/accounts in the knownAccounts structure is expected to be below 1000 entries, else the transaction will be rejected.

### Sample Requests

```
{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "bor_sendRawTransactionConditional",
    "params": [
        "0x2815c17b00...",
        {
            "blockNumberMax": 12345,
            "knownAccounts": {
                "0xadd1": "0xfedc....",
                "0xadd2": { 
                    "0x1111": "0x1234...",
                    "0x2222": "0x4567..."
                }
            }     
        } 
    ]
}

// A curl request will look something like
curl localhost:8545 -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0", "id": 1, "method":"bor_sendRawTransactionConditional", "params": ["0x2815c17b00...", {"knownAccounts": {"0xadd1": "0xfedc...", "0xadd2": {"0x1111": "0x1234...", "0x2222": "0x4567..."}}, "blockNumberMax": 12345, "blockNumberMin": 12300, "timestampMax": 1700000000, "timestampMin": 1600000000}]}'
```

### Sample Responses

```
// A successful response will contain the transaction hash
// For example

{"jsonrpc":"2.0","id":1,"result":"0xd772ef64b9b56bb1b6773f08dbad8f6285f241e2f723a13e6b380c6d3749637b"}


// An unsuccessful response will contain an error with code -32003 or -32005 (or some other code) with an explanation
// For example

{"jsonrpc":"2.0","id":1,"error":{"code":-32003,"message":"out of block range. err: current block number 201 is greater than maximum block number: 55"}}

{"jsonrpc":"2.0","id":1,"error":{"code":-32003,"message":"out of time range. err: current block time 1690287327 is less than minimum timestamp: 0xc019eee948"}}

{"jsonrpc":"2.0","id":1,"error":{"code":-32003,"message":"storage error. err: Storage Trie is nil for: 0xAdd1Add1aDD1aDD1aDd1add1add1AdD"}}

{"jsonrpc":"2.0","id":1,"error":{"code":-32005,"message":"limit exceeded. err: an incorrect list of knownAccounts"}}

{"jsonrpc":"2.0","id":1,"error":{"code":-32005,"message":"limit exceeded. err: number of slots/accounts in KnownAccounts 1100 exceeds the limit of 1000"}}

{"jsonrpc":"2.0","id":1,"error":{"code":-32000,"message":"transaction type not supported"}}
```

## Backward Compatibility

This PIP has no effect on the consensus rules of the network and aims to add one additional API only which is backward compatible.


## Security Considerations

- Requiring validators to check this knownAccounts list before execution could exacerbate the resource usage issue caused by reading large amounts of state. Effectively, a submitter to this endpoint could cause high CPU usage for a validator without ever paying gas, also resulting in denial of service.
    - This is mitigated by adding a condition (which is also mentioned in the spec) that the number of slots/addresses in known accounts should be less than 1000.
- A validator isn’t strictly required to follow this protocol, so a validator could insert a transaction before the bundle which changes the gas characteristics of the bundle, and thus causing issues for the relayer which has submitted the bundle. In effect they could create a scenario where the relayer cannot be fully reimbursed for the gas they pay.
    - This technical issue already exists without this endpoint, and, in any event, the relayer could just stop using that validator.


## References

- [ERC-4337: Account Abstraction Using Alt Mempool](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-4337.md)
- [Integration API for EIP-4337 bundler with an L2 validator/sequencer](https://notes.ethereum.org/@yoav/SkaX2lS9j)

## Reference Implementation

- [New RPC endpoint (bor_sendRawTransactionConditional) to support EIP-4337 Bundled Transactions](https://github.com/maticnetwork/bor/pull/945)

## Copyright
All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
