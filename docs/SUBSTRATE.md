# Polkadot / Substrate — ZChain SCALE Model

[← Model matrix](MODEL_MATRIX.md) · [Methodology](METHODOLOGY.md)

**Current maturity: ADAPTER_READY.**

Substrate-based chains use SCALE encoding, which makes them a distinct serialization family rather than another JSON workload.

## What we want to compress

- SCALE-encoded blocks;
- extrinsics;
- storage proofs;
- state-sync / snapshot batches;
- selected RPC payloads as a separate benchmark surface.

## Why a dedicated model

SCALE compact integers, repeated type layouts and deterministic field ordering create structural information that a blockchain-aware model can exploit. Hashes, signatures and other cryptographic fields are natural opaque candidates.

## Proposed integration

```text
SCALE serializer
   ├── compact integers / tags / repeated layout -> STRUCTURAL
   ├── hashes / signatures                      -> OPAQUE
   └── runtime-known repeated identifiers       -> REFERENCES
```

The serializer should provide boundaries directly; ZChain should not parse arbitrary SCALE bytes after the fact if the runtime already knows the schema.

## Benchmark gate

Promotion to `SUPPORTED` requires real SCALE blocks/extrinsics, exact round trips, RAW/v4/Speed/schema comparison, optimized native timing and a documented memory/latency profile.

**No Substrate-specific performance number is claimed yet.**
