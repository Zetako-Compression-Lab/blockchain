# TRON — ZChain Integration Model

[← Model matrix](MODEL_MATRIX.md) · [Methodology](METHODOLOGY.md)

**Current maturity: ADAPTER_READY.**

TRON is a separate integration target because its block / transaction payloads and address conventions should be validated on their own corpus rather than inheriting an Ethereum performance claim.

## Candidate payloads

- block and transaction RPC responses;
- serialized transaction batches;
- receipts / execution results where applicable;
- archive and export datasets.

## Proposed model split

- transaction and block structure, repeated field names / tags → **STRUCTURAL**;
- signatures, hashes and cryptographic identifiers → **OPAQUE**;
- repeated caller-known identifiers → **REFERENCES**;
- address representations measured explicitly rather than assumed equivalent to EVM hex fields.

## Benchmark gate

The first public TRON benchmark should name the exact RPC or native serialization, use a real corpus, validate exact round trips and compare RAW, v4, Speed and the specialized model on identical bytes.

**No TRON-specific performance number is claimed yet.**
