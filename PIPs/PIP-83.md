---
PIP: 83
Title: Embed EIP-1559 Gas Parameters in Block Header Extra Field
Description: Encode gas target and base fee change denominator in block header extra data for post-Giugliano blocks
Author: Pratik Patil (@pratikspatil024)
Discussion: https://forum.polygon.technology/t/<tbd>
Status: Draft
Type: Core
Date: 2026-03-17
---

## Abstract

With the introduction of [PIP-79](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-79.md), block producers on Polygon PoS can configure EIP-1559 parameters (`gasTarget` and `baseFeeChangeDenominator`) within bounded ranges rather than relying on hardcoded values. However, the parameters each producer actually uses are not recorded on-chain, making them invisible to RPC consumers, block explorers, and off-chain analytics.

This PIP proposes embedding two new optional fields — `GasTarget` and `BaseFeeChangeDenominator` — in the RLP-encoded `BlockExtraData` structure within the block header's `Extra` field, starting from the Giugliano hard fork. Additionally, a new RPC method `bor_getBlockGasParams` is introduced, and the existing `eth_getBlockByNumber` / `eth_getBlockByHash` methods are extended with an optional `borExtra` parameter to surface these values.

## Motivation

* **Transparency:** PIP-79 allows producers to tune EIP-1559 parameters within ±5% boundaries, but provides no on-chain record of the values actually used. Without visibility into per-block `gasTarget` and `baseFeeChangeDenominator`, it is difficult for network participants to verify, audit, or reason about producer fee-setting behavior.
* **Observability for tooling:** Block explorers, fee estimators, MEV searchers, and analytics platforms need access to these parameters to accurately model fee market dynamics. Currently, recovering these values requires reverse-engineering the base fee calculation from consecutive blocks — an error-prone and imprecise approach.
* **Reduced reliance on off-chain data:** Embedding parameters directly in the block header makes them available through standard node RPC without requiring external data sources or secondary indexing infrastructure.
* **Ecosystem alignment:** This follows the same pattern established by `TxDependency` data already present in `BlockExtraData`, extending the existing structure rather than introducing a new mechanism.

## Specification

### 1) BlockExtraData structure extension

The existing `BlockExtraData` structure, RLP-encoded in the block header's `Extra` field (between the vanity bytes and seal), is extended with two new optional fields:

```go
type BlockExtraData struct {
    ValidatorBytes           []byte
    TxDependency             [][]uint64
    GasTarget                *uint64    `rlp:"optional"`
    BaseFeeChangeDenominator *uint64    `rlp:"optional"`
}
```

* **`GasTarget`** (`*uint64`, optional): The EIP-1559 gas target used by the block producer when computing the block's `baseFee`. Calculated from the parent header as: `parent.GasLimit * targetGasPercentage / 100` (post-Dandeli) or `parent.GasLimit / elasticityMultiplier` (pre-Dandeli).
* **`BaseFeeChangeDenominator`** (`*uint64`, optional): The denominator applied in the EIP-1559 base fee adjustment formula. Controls how aggressively the base fee responds to deviations between actual gas usage and the gas target.

Both fields use RLP optional encoding (`rlp:"optional"`) to maintain backward compatibility — pre-Giugliano blocks decode without these fields present.

### 2) Block preparation (consensus)

During block preparation (`Prepare`), for blocks at or after the Giugliano activation height, the consensus engine **MUST** compute and embed both parameters using the parent header:

```go
func setGiuglianoExtraFields(header, parent *types.Header, blockExtraData *types.BlockExtraData) {
    gasTarget := eip1559.CalcGasTarget(chainConfig, parent)
    bfcd := params.BaseFeeChangeDenominator(borConfig, parent.Number)
    blockExtraData.GasTarget = &gasTarget
    blockExtraData.BaseFeeChangeDenominator = &bfcd
}
```

The populated `BlockExtraData` is then RLP-encoded and written into the header's `Extra` field alongside the existing vanity bytes, validator bytes, transaction dependency data, and seal.

### 3) Block verification (consensus)

During header verification, for post-Giugliano blocks, validators **MUST** check that both `GasTarget` and `BaseFeeChangeDenominator` are present in the decoded `BlockExtraData`:

```go
if isGiugliano(header.Number) {
    gasTarget, bfcd := header.GetBaseFeeParams(chainConfig)
    if gasTarget == nil || bfcd == nil {
        return errMissingGiuglianoFields
    }
}
```

**Note:** Verification checks for the *presence* of these fields, not their correctness against a specific calculation. These are self-reported values from the block producer, reflecting the parameters they chose within the PIP-79 bounded range. The actual base fee validity is enforced separately by the ±5% boundary check introduced in PIP-79.

### 4) RPC enhancements

#### a) New method: `bor_getBlockGasParams`

Returns the EIP-1559 gas parameters embedded in a block header's extra data.

**Parameters:**
- `blockNrOrHash` — block number or hash

**Response:**
```json
{
  "gasTarget": "0x...",
  "baseFeeChangeDenominator": "0x..."
}
```

Both fields are hex-encoded `uint64` values. For pre-Giugliano blocks, the fields will be `null`.

#### b) Extended methods: `eth_getBlockByNumber` / `eth_getBlockByHash`

An optional third parameter `borExtra` (`bool`, default `false`) is added. When set to `true`, the block response includes a `decodedExtra` object containing the parsed `BlockExtraData` fields (gas parameters and transaction dependency metadata).

## Backward Compatibility

This PIP introduces a **consensus-breaking change** that requires the Giugliano hard fork to activate.

* **Pre-Giugliano blocks** are unaffected. The `GasTarget` and `BaseFeeChangeDenominator` fields use RLP optional encoding, so existing blocks decode correctly without them.
* **Post-Giugliano blocks** are required to include both fields. Nodes running pre-Giugliano software will fail to validate post-fork blocks due to the presence check in header verification.
* **RPC compatibility:** The `borExtra` parameter is optional and defaults to `false`, so existing RPC consumers are unaffected. The new `bor_getBlockGasParams` method is additive.

## Security Considerations

* **Self-reported values:** The embedded `GasTarget` and `BaseFeeChangeDenominator` are self-reported by the block producer. They reflect the parameters the producer claims to have used, but actual base fee validity is enforced by the ±5% boundary validation from PIP-79. Consumers should treat these values as informational metadata, not as consensus-enforced constraints on the calculation itself.
* **Extra field size:** Adding two `uint64` fields (up to 8 bytes each when RLP-encoded) has negligible impact on block header size and propagation.
* **RLP decoding robustness:** The optional RLP encoding ensures that decoding failures on pre-Giugliano blocks do not cause node crashes — missing fields default to `nil`.

## References

* [PIP-79: Bounded-Range Validation for Configurable EIP-1559 Parameters](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-79.md)
* [EIP-1559: Fee market change for ETH 1.0 chain](https://eips.ethereum.org/EIPS/eip-1559)
* [Reference Implementation (Bor PR #2135)](https://github.com/0xPolygon/bor/pull/2135)

## Copyright

All copyrights and related rights in this work are waived under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
