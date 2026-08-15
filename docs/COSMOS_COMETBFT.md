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

## Published results

Representative Apple M4 native C release results:

| Workload | v4 encode | v4 decode | v4 behavior | Speed encode | Speed decode | Size delta vs v4 |
|---|---:|---:|---|---:|---:|---:|
| `block` JSON | 49.72 | 31.47 | 52.66% savings class | 58.18 | 34.19 | -1.32% |
| `block_results` JSON | **120.74** | **77.23** | **84.87% savings** | **143.42** | **85.77** | +1.03% |
| `commit` JSON | 52.20 | 31.40 | 52.75% savings class | 57.64 | 34.05 | -1.12% |
| `consensus_params` | 39.58 | 29.95 | small structured payload | 44.81 | 25.94 | +2.10% |
| `validators` | 56.07 | 39.64 | 56.90% savings class | 63.34 | 35.16 | +1.02% |

Throughput is MiB/s. The clearest public result is `block_results`: strong density and high encode throughput on a real CometBFT RPC payload class.

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

The current public evidence covers CometBFT JSON-RPC payloads. It does not yet establish Cosmos SDK protobuf, state-sync or validator-network performance.

Detailed corpus methodology remains in [COMETBFT_COSMOS_BENCHMARKS.md](COMETBFT_COSMOS_BENCHMARKS.md).
