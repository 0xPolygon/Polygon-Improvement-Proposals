---
PIP: 78  
Title: Block Producer Planned Downtime Transaction  
Description: Introduce a transaction that allows a block producer to gracefully rotate block production to another producer during planned downtime  
Author: Angel Valkov (@avalkov), Jerry Chen (fchen@polygon.technology)  
Status: Final  
Type: Core  
Date: 2025-10-01  
---

## Abstract

This proposal introduces a new `heimdallv2` transaction type that allows a Bor block producer to declare a planned downtime window. The mechanism enables graceful rotation of block production to other producers without disrupting span execution or network liveness.

## Motivation

With the introduction of [VEBloP](https://github.com/0xPolygon/Polygon-Improvement-Proposals/blob/main/PIPs/PIP-64.md), a single block producer is responsible for producing an entire Bor span, which can last up to approximately four hours. While this improves efficiency and predictability, it also introduces operational challenges.

Specifically, the active block producer may need to perform urgent configuration changes, apply updates, or schedule planned maintenance during its assigned span. Without a dedicated mechanism to handle such cases, these actions risk causing block production gaps or forcing ungraceful failover.

To address this, this proposal introduces a new transaction type that allows a block producer to proactively signal planned downtime and safely hand off block production responsibilities.

## Specification

The planned downtime transaction must be signed and submitted by the block producer that intends to declare its own downtime window.

```bash
heimdalld tx bor producer-downtime \
  --producer-address=<producer-address> \
  --start-timestamp-utc=<start-timestamp-utc> \
  --end-timestamp-utc=<end-timestamp-utc> \
  --calc-only=<true|false>
```

The downtime window is specified in terms of UTC timestamps. Before the transaction is broadcast, these timestamps are deterministically converted into a Bor block range.

If the --calc-only=true flag is provided, the command performs the timestamp-to-block conversion and prints the resulting block range without submitting the transaction. This allows operators to preview the effect of the downtime declaration.

The on-chain message types are defined as follows:

```go
type MsgSetProducerDowntime struct {
	Producer      string
	DowntimeRange BlockRange
}

type BlockRange struct {
	StartBlock uint64
	EndBlock   uint64
}
```

### Constraints and Validation Rules

To prevent misuse and ensure predictable behavior, the transaction is subject to several constraints. All values are expressed in terms of Bor blocks.

```go
const (
	PlannedDowntimeMinimumTimeInFuture = 150
	PlannedDowntimeMaximumTimeInFuture = 100 * DefaultSpanDuration
	PlannedDowntimeMinRange            = 150
	PlannedDowntimeMaxRange            = 14 * DefaultSpanDuration
)
```

These limits enforce that:

- Planned downtime must be declared sufficiently in advance.
- Downtime cannot be scheduled arbitrarily far into the future.
- The downtime window must fall within a bounded duration.

### Runtime Behavior

If a downtime transaction is submitted for a producer that is currently assigned to the active span, a new span is immediately generated. The active span is overridden and reassigned to the first available eligible block producer.

If the transaction is submitted for a producer that is not currently producing blocks, that producer is excluded from block producer selection for the specified downtime range.

This behavior ensures that block production continues uninterrupted while respecting the declared downtime intent of the producer.

## Copyright

All copyrights and related rights in this work are waived under [CCO 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/legalcode).
