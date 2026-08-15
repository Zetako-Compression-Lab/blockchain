# ZChain C Real Benchmark: v3 vs v4

Date: 2026-08-15  
CPU: Apple M4  
Scope: C codec only, in memory. File loading, CSV writing, process startup, and hashing are outside the timed section.

## Summary

ZChain v4 keeps the **same compressed sizes as v3 on this public corpus**, so the measured compression output and savings are unchanged in this benchmark.

On the full public blockchain corpus, release v4 improves encode throughput from **74.05 MiB/s to 112.54 MiB/s**, a **1.52× encode speedup**. Decode is effectively unchanged at about **68 MiB/s**.

## Corpus

| Group | Use case |
|---|---|
| Agave / Solana | Markdown report and ZChain source payloads |
| Reth / Ethereum | Blocks JSON and receipts JSON |
| CometBFT / Cosmos | Blocks, block results, commits, validators, consensus params |
| Solana RPC | `getBlock` full JSON, signatures, commitments, production, transactions |

Total corpus: **64 files, 30,017,805 input bytes**.

## Release comparison

| Kind | v3 encode MiB/s | v4 encode MiB/s | Encode speedup | v3 decode MiB/s | v4 decode MiB/s | Decode speedup | Savings | Bytes match |
|---|---:|---:|---:|---:|---:|---:|---:|---|
| all | 74.05 | 112.54 | 1.52× | 68.00 | 68.00 | 1.00× | 82.14% | yes |
| agave_report_markdown | 28.89 | 46.11 | 1.60× | 27.90 | 33.59 | 1.20× | 51.88% | yes |
| agave_zchain_sources | 34.30 | 60.02 | 1.75× | 33.89 | 43.16 | 1.27× | 62.73% | yes |
| ethereum_block_json | 45.44 | 71.24 | 1.57× | 43.80 | 42.43 | 0.97× | 71.04% | yes |
| ethereum_receipts_json | 98.55 | 141.35 | 1.43× | 92.73 | 91.27 | 0.98× | 88.18% | yes |
| cometbft_block_json | 32.55 | 47.27 | 1.45× | 31.71 | 31.40 | 0.99× | 52.66% | yes |
| cometbft_block_results_json | 83.32 | 120.46 | 1.45× | 78.43 | 77.47 | 0.99× | 84.87% | yes |
| cometbft_commit_json | 32.65 | 51.38 | 1.57× | 31.81 | 31.24 | 0.98× | 52.75% | yes |
| cometbft_consensus_params_json | 7.90 | 37.45 | 4.74× | 8.03 | 29.33 | 3.65× | 28.50% | yes |
| cometbft_validators_json | 32.39 | 55.80 | 1.72× | 30.43 | 39.49 | 1.30× | 56.90% | yes |
| solana_getBlock_full_json | 81.87 | 123.36 | 1.51× | 73.96 | 74.32 | 1.00× | 84.18% | yes |
| solana_getBlock_signatures_json | 22.16 | 37.17 | 1.68× | 22.24 | 21.37 | 0.96× | 25.40% | yes |
| solana_getBlockCommitment_json | 4.89 | 44.13 | 9.02× | 4.87 | 41.52 | 8.53× | 31.71% | yes |
| solana_getBlockProduction_json | 23.59 | 37.48 | 1.59× | 22.94 | 22.18 | 0.97× | 28.48% | yes |
| solana_getTransaction_json | 23.11 | 44.95 | 1.95× | 23.00 | 35.12 | 1.53× | 42.72% | yes |

## Aggregate latency

| Build | Encode MiB/s | Decode MiB/s | Encode ns/byte | Decode ns/byte | Encode p50 ms | Encode p95 ms | Encode p99 ms | Decode p50 ms | Decode p95 ms | Decode p99 ms |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| v3 release O3 | 74.05 | 68.00 | 12.88 | 14.03 | 1.1514 | 59.0087 | 64.0800 | 1.2250 | 64.9608 | 72.3379 |
| v4 release O3 | 112.54 | 68.00 | 8.47 | 14.02 | 0.7788 | 38.3375 | 42.8860 | 1.2345 | 64.4914 | 71.0562 |

## Interpretation

The strongest compatibility headline is simple: **same compressed bytes on the tested corpus, same savings, substantially faster encode in release**.

The most credible public examples are Solana full blocks, Ethereum blocks/receipts, and CometBFT block results because they represent real public-chain JSON payloads rather than synthetic microbenchmarks.