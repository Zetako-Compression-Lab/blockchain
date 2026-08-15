# ZChain Benchmarks on Reth / Ethereum Payloads

This experiment adds a standalone ZChain benchmark harness to a Reth checkout and measures compression on real Ethereum JSON-RPC payloads.

The first integration is intentionally outside the Reth node runtime. It provides reproducible public metrics before touching networking, storage, sync, or consensus-adjacent paths.

## Why Reth

Reth is a Rust Ethereum execution client with a modular codebase. That makes it a useful target for ZChain experiments on:

- JSON-RPC block payloads;
- receipt payloads;
- future RLP block or transaction batches;
- future snapshot/export sidecars;
- future P2P payload experiments.

## Added tool

```text
tools/reth-zchain-bench/
```

The tool includes the local ZChain v3 Rust port by path and benchmarks every file in an input directory. It writes a CSV containing:

- raw bytes;
- ZChain bytes;
- compression ratio;
- savings percentage;
- encode/decode time;
- encode/decode throughput;
- input and decoded SHA-256;
- round-trip status.

## Fetch dataset

```bash
cd tools/reth-zchain-bench
./fetch-ethereum-payloads.sh benchmark-data/ethereum-json
```

The script uses `ETH_RPC_URL` when set, otherwise it uses the configured public Ethereum RPC endpoint.

The documented sample contains:

- `eth_getBlockByNumber` with full transactions for five consecutive blocks;
- `eth_getBlockReceipts` for the same five blocks.

## Run benchmark

```bash
cd tools/reth-zchain-bench
cargo run --release -- benchmark-data/ethereum-json benchmark-data/reth-zchain-results.csv
```

The use of `--release` is important: these figures are intended to represent optimized benchmark execution rather than development-build integration overhead.

## Observed results

Local run on 2026-08-15:

| Payload | Files | Raw bytes | ZChain bytes | Ratio | Savings | Encode | Decode | Round trip |
|---|---:|---:|---:|---:|---:|---:|---:|---|
| `eth_getBlockByNumber` | 5 | 1,256,590 | 363,968 | 0.2896 | **71.04%** | 31.578 ms | 39.715 ms | **5/5 OK** |
| `eth_getBlockReceipts` | 5 | 1,651,917 | 195,266 | 0.1182 | **88.18%** | 13.306 ms | 18.803 ms | **5/5 OK** |
| **Total** | **10** | **2,908,507** | **559,234** | **0.1923** | **80.77%** | **44.884 ms** | **58.518 ms** | **10/10 OK** |

## Derived effective throughput

Using total raw bytes divided by aggregate measured encode/decode time:

| Scope | Effective throughput |
|---|---:|
| Encode | **~61.8 MiB/s** |
| Decode | **~47.4 MiB/s** |

These values are **derived from this specific release run and dataset**. They are useful public evidence for this workload, but they are not universal throughput guarantees for every ZChain payload or integration.

## Interpretation

This is a strong public use case for ZChain on Ethereum execution-layer data:

- receipts JSON compresses very strongly because logs, addresses, topics, bloom filters, and hex fields are structurally repetitive;
- full block JSON also compresses strongly despite transaction entropy;
- the documented dataset achieves **80.77% aggregate savings**;
- all ten payloads reconstruct successfully with SHA-256 equality;
- the benchmark is executed in release mode.

## Benchmark boundary

This benchmark measures a standalone Reth-adjacent harness. It does **not** yet prove:

- Reth node runtime integration;
- Reth P2P network throughput;
- database/storage-path performance;
- sync-path latency;
- mainnet-wide bandwidth savings.

Those require separate integration-specific experiments.

## Next integration step

A deeper Reth experiment should add an optional native payload source:

1. pull blocks from a Reth database or RPC provider;
2. encode canonical block bodies and receipts as RLP or Reth database codec bytes;
3. reuse the same ZChain metrics to compare JSON vs RLP vs database payloads;
4. separately benchmark any future storage or networking hook in release mode.

This would extend the current JSON-RPC proof into a deeper Reth storage/networking evaluation while preserving clear measurement boundaries.