# EVM L2 and EVM-Compatible Chains — ZChain

[← Product evolution](../README.md) · [Ethereum](ETHEREUM.md) · [Model matrix](MODEL_MATRIX.md)

Base, Arbitrum, Optimism, Polygon, BNB Chain and Avalanche share enough Ethereum/EVM-facing JSON-RPC structure that ZChain does **not** need to pretend each chain already has a completely separate codec.

## Current approach

For JSON-RPC blocks, receipts and traces that contain the same `0x`-heavy structural pattern, the current integration baseline is the negotiated **Ethereum hex family model**.

That gives these chains an immediate integration path without creating six nominally different codecs that all run the same logic.

## What we compress

Candidate surfaces include:

- full block JSON-RPC responses;
- transaction and receipt responses;
- traces;
- logs and event exports;
- archive / indexer data;
- future native serialization paths specific to each client or rollup stack.

## Why this page does not copy Ethereum benchmark numbers

Ethereum mainnet evidence proves behavior on the documented Reth Ethereum corpus. It does **not** prove the same ratio or throughput on Base, Arbitrum, Optimism, Polygon, BNB or Avalanche payload distributions.

The correct public position is:

> **The EVM JSON-hex integration model is reusable across compatible chains; performance claims remain chain-specific.**

## When a chain gets its own model

A separate negotiated model is justified only when the chain's actual workload demonstrates a useful difference, for example:

- rollup-specific batch data;
- blob / DA payload patterns;
- custom receipt or trace distributions;
- sequencer-specific storage formats;
- chain-specific snapshot or archive structures.

Until a benchmark demonstrates that difference, one EVM-family model is simpler and more credible.

## Benchmark gate

For each chain, promotion to a chain-specific `SUPPORTED` page requires:

1. a named real corpus;
2. exact RPC or native serialization type;
3. RAW / v4 / Speed / negotiated-model comparison;
4. exact round trip;
5. native release timing;
6. no reuse of Ethereum numbers as if they were measured locally.

## Current status

**EVM JSON-hex family: SUPPORTED integration model.**

**Individual chain performance: benchmark pending.**
