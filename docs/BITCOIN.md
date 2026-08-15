# Bitcoin — ZChain Integration Model

[← Product evolution](../README.md) · [Model matrix](MODEL_MATRIX.md) · [Methodology](METHODOLOGY.md)

Bitcoin is the next major native serialization target after Ethereum because its wire and storage structures are fundamentally different from JSON-heavy RPC workloads.

**Current maturity: ADAPTER_READY.** No Bitcoin-specific ZChain benchmark is presented here yet.

## What we want to compress

Relevant Bitcoin surfaces include:

- raw block batches / `blk*.dat`-style data;
- transaction batches;
- mempool transfer or export datasets;
- block / transaction archive movement;
- selected RPC data as a separate, higher-level workload.

## Why a Bitcoin-specific model is useful

A Bitcoin block mixes compact structure with cryptographic and script-heavy material:

- block header fields;
- transaction counts and compact integers;
- transaction inputs / outputs;
- txids / wtxids;
- scripts;
- signatures;
- witness data.

Treating all of those bytes with one generic probability model is unlikely to be optimal.

## Proposed negotiated model

```text
Bitcoin serializer / block reader
          │
          ├── headers, counts, amounts, compact integers -> STRUCTURAL
          ├── txids / wtxids / signatures               -> OPAQUE
          ├── repeated known values                      -> REFERENCES
          └── scripts / witness                          -> dedicated sub-profile candidate
```

The runtime should identify those boundaries directly. ZChain should **not scan an arbitrary block looking for hashes or scripts**.

## Benchmark plan

Before the profile can become `SUPPORTED`, the public benchmark should contain:

1. real raw blocks from multiple heights / block shapes;
2. transaction-only batches;
3. witness-heavy and non-witness mixes;
4. RAW, v4, Speed and Bitcoin-schema comparisons on the exact same bytes;
5. exact round-trip validation;
6. compressed bytes, savings, encode/decode MiB/s, ns/byte and latency distribution;
7. memory per worker;
8. explicit separation between raw block serialization and JSON-RPC data.

## What we will not claim yet

There is currently no public claim for Bitcoin block compression ratio, node bandwidth reduction or block-validation latency. The page exists to make the **integration contract and benchmark target explicit before numbers are published**.
