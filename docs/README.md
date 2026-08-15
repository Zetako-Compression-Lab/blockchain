# ZChain Public Documentation

This directory is organized as the public technical documentation for ZChain.

## Start here

- [Product evolution: v3 → v4 → Speed_First](../README.md)
- [Full v3 / v4 / Speed_First benchmark report](ZCHAIN_V3_V4_SPEED_FIRST_C_REAL_BENCH_REPORT.md)
- [Public claims and measurement boundaries](PUBLIC_CLAIMS.md)

## Blockchain pages

### Ethereum / Reth

[Open Ethereum / Reth benchmarks](zchain-reth-benchmarks.md)

Covers full block JSON and receipts, release benchmark methodology, SHA-256 round-trip validation, measured savings, performance, why Ethereum execution-layer payloads are interesting for compression, and future RLP/storage/network experiments.

### Solana mainnet

[Open Solana mainnet benchmarks](SOLANA_MAINNET_BENCHMARKS.md)

Covers `getBlock`, signatures, commitments, block production and transaction payloads, with v3/v4/Speed_First results and the practical value of reducing large Solana RPC responses.

### Agave

[Open Agave integration](AGAVE_INTEGRATION.md)

Covers the validator-facing integration architecture, fail-open design, benchmark tooling, shred shadow probe, measurement boundaries and what must be validated before a production transport/storage path.

### Cosmos / CometBFT

[Open CometBFT / Cosmos benchmarks](COMETBFT_COSMOS_BENCHMARKS.md)

Covers block, block results, commits, validators and consensus parameters from Cosmos Hub RPC data.

## Codec comparisons

[Open selected compression comparison](COMPRESSION_COMPARISON.md)

Workload-specific comparison of ZChain against gzip, Brotli and LZMA2/7zip using stated presets and payloads.

## Benchmark methodology

- [Native C benchmark report](ZCHAIN_C_REAL_BENCH_REPORT.md)
- [v3 vs v4 compatibility comparison](ZCHAIN_V3_VS_V4_C_REAL_BENCH_REPORT.md)
- [v3 vs v4 vs Speed_First](ZCHAIN_V3_V4_SPEED_FIRST_C_REAL_BENCH_REPORT.md)

## Reading the results correctly

ZChain reports native codec performance separately from integration-harness performance. Numbers are scoped to their dataset, hardware, build flags and profile. A compression saving observed on RPC or benchmark data is not automatically claimed as an equivalent production network or storage saving without a separately validated integration path.
