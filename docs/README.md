# ZChain Blockchain Documentation

[← Main product page](../README.md)

This directory is organized as a public engineering and product map rather than as a chronological dump of experiments.

## Start here

- [Ethereum / Reth](ETHEREUM.md)
- [EVM L2 / EVM-compatible chains](EVM_L2.md)
- [Solana](SOLANA.md)
- [Agave validator integration](AGAVE.md)
- [Cosmos / CometBFT](COSMOS_COMETBFT.md)
- [Bitcoin](BITCOIN.md)
- [Model matrix](MODEL_MATRIX.md)
- [Benchmark methodology](METHODOLOGY.md)
- [ZChain vs established codecs](COMPRESSION_COMPARISON.md)

## Latest reference benchmark

- [ZChain Blockchain C 1.2.0 Research — Apple M4 benchmark report](ZCHAIN_BLOCKCHAIN_C_1_2_0_M4_BENCH_REPORT.md)

This is the current reference run for the supported/public corpus summaries. It includes Ethereum Reth JSON, Solana mainnet RPC JSON, CometBFT CosmosHub RPC JSON and an Agave ledger-source corpus, plus the `ETHEREUM_SCHEMA` smoke status.

## Adapter-ready chain pages

- [Polkadot / Substrate](SUBSTRATE.md)
- [Cardano](CARDANO.md)
- [Sui](SUI.md)
- [Aptos](APTOS.md)
- [TRON](TRON.md)

These pages deliberately distinguish **measured results** from **integration models whose chain-specific benchmarks are still pending**.

## Detailed engineering evidence

The older detailed reports remain available because they show how the product evolved and preserve reproducible evidence:

- [v3 vs v4 compatibility report](ZCHAIN_V3_VS_V4_C_REAL_BENCH_REPORT.md)
- [v3 → v4 → Speed report](ZCHAIN_V3_V4_SPEED_FIRST_C_REAL_BENCH_REPORT.md)
- [native C benchmark report](ZCHAIN_C_REAL_BENCH_REPORT.md)
- [historical Ethereum / Reth benchmark evidence](zchain-reth-benchmarks.md)
- [historical Solana mainnet benchmark evidence](SOLANA_MAINNET_BENCHMARKS.md)
- [historical Agave integration evidence](AGAVE_INTEGRATION.md)
- [historical Agave benchmark notes](zchain-agave-benchmarks.md)
- [historical CometBFT / Cosmos evidence](COMETBFT_COSMOS_BENCHMARKS.md)
- [public claim boundaries](PUBLIC_CLAIMS.md)

## Reading rule

The short ecosystem pages explain **what ZChain is trying to do and what has been proven**. The latest reference report provides the current benchmark snapshot, while the older detailed reports preserve the evolution. If two reports describe different generations, always quote the generation/profile name with the number.
