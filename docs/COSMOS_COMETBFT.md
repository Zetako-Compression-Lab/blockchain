# Cosmos / CometBFT — ZChain

[← Product evolution](../README.md) · [Methodology](METHODOLOGY.md)

Cosmos / CometBFT is a useful ZChain target because the public RPC surface contains several different structured payload families rather than one dominant block format.

## What we compress

The public corpus includes:

- `block`;
- `block_results`;
- `commit`;
- `validators`;
- `consensus_params`.

These payloads combine consensus metadata, signatures, validator information, events and application results.

## Why it matters

Potential use cases include:

- RPC bandwidth reduction;
- validator / indexer exports;
- consensus-result movement between infrastructure services;
- archival and analytical datasets;
- future state-sync or protobuf-native experiments.

## Latest supported profile result

Latest reference run: Apple M4, native C release, `-O3 -DNDEBUG -mcpu=native`, `cometbft`, 50 iterations on the CosmosHub RPC corpus.

| Files | Raw bytes | ZChain bytes | Savings | Encode | Decode | Encode ns/B | Decode ns/B |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 25 | 968,332 | **295,048** | **69.53%** | **67.29 MiB/s** | **43.19 MiB/s** | 14.173 | 22.081 |

All benchmarked files round-trip exactly, and the package passed the test and sanitizer gates.

[Open the full 1.2.0 M4 benchmark report →](ZCHAIN_BLOCKCHAIN_C_1_2_0_M4_BENCH_REPORT.md)

## Historical profile evolution

Earlier public measurements remain available because they show how the generic codec behaved across individual CometBFT RPC classes before the current blockchain engine.

| Workload | v4 encode | v4 decode | v4 behavior | Speed encode | Speed decode | Size delta vs v4 |
|---|---:|---:|---|---:|---:|---:|
| `block` JSON | 49.72 | 31.47 | 52.66% savings class | 58.18 | 34.19 | -1.32% |
| `block_results` JSON | **120.74** | **77.23** | **84.87% savings** | **143.42** | **85.77** | +1.03% |
| `commit` JSON | 52.20 | 31.40 | 52.75% savings class | 57.64 | 34.05 | -1.12% |
| `consensus_params` | 39.58 | 29.95 | small structured payload | 44.81 | 25.94 | +2.10% |
| `validators` | 56.07 | 39.64 | 56.90% savings class | 63.34 | 35.16 | +1.02% |

These figures are historical per-workload profile measurements. The 1.2.0 aggregate reference run above is the current supported-profile summary.

## Current model status

`cometbft-rpc-json` is **SUPPORTED**.

The next distinct serialization family is **Cosmos SDK protobuf**, which is currently **ADAPTER_READY** rather than supported. Protobuf tags, varints, repeated fields, hashes and signatures should eventually be modeled using an explicit protobuf-aware integration rather than by pretending JSON and protobuf are the same workload.

## Future protobuf model

A native Cosmos model should consider:

- protobuf field tags and varints as structural bytes;
- repeated message layouts as context-rich structure;
- signatures and hashes as opaque data;
- known repeated identifiers as references where the runtime can provide identity;
- state-sync and snapshot payloads as a separate benchmark surface.

## Claim boundary

The current public evidence covers CometBFT CosmosHub JSON-RPC payloads. It does not yet establish Cosmos SDK protobuf, state-sync or validator-network performance.

Detailed corpus methodology remains in [COMETBFT_COSMOS_BENCHMARKS.md](COMETBFT_COSMOS_BENCHMARKS.md).
