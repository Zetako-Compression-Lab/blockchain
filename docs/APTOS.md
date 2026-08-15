# Aptos — ZChain BCS Model

[← Model matrix](MODEL_MATRIX.md) · [Methodology](METHODOLOGY.md)

**Current maturity: ADAPTER_READY.**

Aptos is another BCS-family target, but its benchmark must remain chain-specific even if implementation ideas are shared with Sui.

## Candidate payloads

- BCS transactions;
- blocks / transaction batches;
- account and state export data;
- RPC payloads as a separate workload;
- snapshot / state-sync batches.

## Proposed model

- BCS structural tags, lengths and repeated layouts → **STRUCTURAL**;
- transaction hashes, signatures, public keys and other cryptographic fields → **OPAQUE**;
- caller-known repeated identifiers → **REFERENCES**.

Sharing a BCS-aware engine with Sui is reasonable; sharing Sui benchmark numbers is not.

## Benchmark gate

Promotion to `SUPPORTED` requires a real Aptos BCS corpus, exact reconstruction, optimized native measurement and direct RAW/v4/Speed/schema comparison.

**No Aptos-specific compression or throughput figure is claimed yet.**
