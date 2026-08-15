# Sui — ZChain BCS Model

[← Model matrix](MODEL_MATRIX.md) · [Methodology](METHODOLOGY.md)

**Current maturity: ADAPTER_READY.**

Sui is a candidate for a BCS-aware ZChain model. The target is native serialized data, not simply another generic JSON preset.

## Candidate payloads

- BCS transaction and object batches;
- checkpoint / block-like data exports;
- RPC transaction and object responses as a separate higher-level workload;
- snapshot and state-transfer batches.

## Proposed schema-assisted split

- BCS lengths, enum tags, deterministic field structure → **STRUCTURAL**;
- object / transaction digests, signatures, keys → **OPAQUE**;
- runtime-known repeated identifiers → **REFERENCES**.

The integration should ideally sit beside the serializer so field identity is known before bytes are flattened into one buffer.

## Benchmark gate

A Sui profile becomes `SUPPORTED` only after a real BCS corpus is published or reproducibly fetched, exact round trips pass, and the same bytes are compared across RAW, v4, Speed and the BCS-aware model.

**No Sui-specific performance claim is published yet.**
