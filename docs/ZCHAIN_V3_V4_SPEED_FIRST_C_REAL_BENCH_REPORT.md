# ZChain C Real Benchmark: v3 vs v4 vs Speed_First

Date: 2026-08-15  
CPU: Apple M4  
Scope: C codec only, in memory. File loading, CSV writing, process startup, and hashing are outside the timed section.

## Summary

`ZChain_C_Speed_First` is the fastest C profile on the current public blockchain corpus, but it is **not bitstream-compatible** with v3/v4. It should be treated as a new speed-oriented frame/profile, not a drop-in replacement for historical streams.

On the full 64-file corpus:

| Codec | Compressed bytes | Savings | Encode MiB/s | Decode MiB/s | Encode vs v4 | Decode vs v4 |
|---|---:|---:|---:|---:|---:|---:|
| v3 compatibility | 5,361,692 | 82.14% | 74.27 | 67.96 | 0.66× | 0.99× |
| v4 compatibility | 5,361,692 | 82.14% | 113.36 | 68.54 | 1.00× | 1.00× |
| Speed_First | 5,457,493 | 81.82% | 126.53 | 74.67 | 1.12× | 1.09× |

Speed_First is **1.70× faster than v3 on encode** and **1.12× faster than v4 on encode**. The aggregate compressed size increases by **1.79% vs v4**.

## Compatibility

| Codec | Stream compatibility | Note |
|---|---|---|
| v3 | v3 baseline | Original compatibility profile |
| v4 | Same compressed bytes as v3 on this corpus | Compatibility-oriented optimized profile |
| Speed_First | Not compatible with v3/v4 bitstreams | Encoder and decoder must be deployed together |

## Release comparison by use case

| Kind | v4 bytes | Speed_First bytes | Size delta vs v4 | v4 encode | Speed encode | Encode vs v4 | v4 decode | Speed decode | Decode vs v4 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| all | 5,361,692 | 5,457,493 | +1.79% | 113.36 | 126.53 | 1.12× | 68.54 | 74.67 | 1.09× |
| agave_report_markdown | 2,887 | 3,197 | +10.74% | 46.87 | 52.67 | 1.12× | 34.15 | 29.93 | 0.88× |
| agave_zchain_sources | 10,537 | 11,156 | +5.87% | 60.61 | 67.39 | 1.11× | 43.50 | 42.11 | 0.97× |
| ethereum_block_json | 363,968 | 373,399 | +2.59% | 71.63 | 79.07 | 1.10× | 42.56 | 45.54 | 1.07× |
| ethereum_receipts_json | 195,266 | 206,234 | +5.62% | 143.14 | 162.55 | 1.14× | 92.83 | 101.13 | 1.09× |
| cometbft_block_json | 102,266 | 100,913 | -1.32% | 49.72 | 58.18 | 1.17× | 31.47 | 34.19 | 1.09× |
| cometbft_block_results_json | 76,170 | 76,956 | +1.03% | 120.74 | 143.42 | 1.19× | 77.23 | 85.77 | 1.11× |
| cometbft_commit_json | 101,372 | 100,239 | -1.12% | 52.20 | 57.64 | 1.10× | 31.40 | 34.05 | 1.08× |
| cometbft_consensus_params_json | 1,237 | 1,263 | +2.10% | 39.58 | 44.81 | 1.13× | 29.95 | 25.94 | 0.87× |
| cometbft_validators_json | 14,003 | 14,146 | +1.02% | 56.07 | 63.34 | 1.13× | 39.64 | 35.16 | 0.89× |
| solana_getBlock_full_json | 4,032,707 | 4,104,616 | +1.78% | 124.35 | 138.55 | 1.11× | 74.99 | 81.98 | 1.09× |
| solana_getBlock_signatures_json | 441,299 | 445,575 | +0.97% | 36.53 | 40.66 | 1.11× | 21.30 | 23.01 | 1.08× |
| solana_getBlockCommitment_json | 560 | 560 | +0.00% | 48.91 | 56.15 | 1.15× | 42.10 | 37.69 | 0.90× |
| solana_getBlockProduction_json | 12,516 | 12,340 | -1.41% | 38.20 | 42.37 | 1.11× | 22.55 | 24.34 | 1.08× |
| solana_getTransaction_json | 6,904 | 6,899 | -0.07% | 46.46 | 53.65 | 1.15× | 35.77 | 31.59 | 0.88× |

## Public positioning

Use **v4** when backward compatibility matters: on this corpus it preserves the v3 compressed bytes while improving encode speed substantially.

Use **Speed_First** when both sides can deploy the new profile together: it gives the best aggregate throughput with a small aggregate compression-ratio cost on this corpus.

The size tradeoff varies by payload type. Speed_First is not universally larger; several CometBFT and Solana groups are slightly smaller than v4.