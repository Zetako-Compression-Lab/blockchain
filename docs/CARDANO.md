# Cardano — ZChain CBOR Model

[← Model matrix](MODEL_MATRIX.md) · [Methodology](METHODOLOGY.md)

**Current maturity: ADAPTER_READY.**

Cardano is a distinct target because CBOR encoding has its own structural grammar, integer representation and container semantics.

## What we want to compress

- CBOR block batches;
- transaction batches;
- witness-heavy payloads;
- archive / export data;
- state / snapshot payloads as a separate model.

## Proposed model split

- CBOR major types, lengths, repeated map/array structure → **STRUCTURAL**;
- transaction hashes, signatures and cryptographic material → **OPAQUE**;
- runtime-known repeated identifiers → **REFERENCES**.

A native integration should emit these classes while serializing CBOR rather than asking ZChain to reverse-engineer the document afterward.

## Why it could help

Structured CBOR fields and repeated transaction layouts may be predictable, while witnesses and hashes are much closer to entropy. Separating those regions is consistent with the architecture already validated in Ethereum-focused research.

## Benchmark gate

Before this model becomes `SUPPORTED`, the public page needs a real Cardano corpus, exact byte reconstruction, optimized native benchmark and comparison against RAW, v4, Speed and the CBOR-aware model.

**No Cardano-specific ratio or throughput is claimed yet.**
