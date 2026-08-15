# Ethereum / Reth — ZChain Benchmark Page

[← Documentation index](README.md) · [Product evolution](../README.md) · [Codec comparison](COMPRESSION_COMPARISON.md)

This page documents how ZChain behaves on **real Ethereum execution-layer JSON-RPC payloads** collected for a standalone Reth-adjacent benchmark.

## What we compress

The documented Ethereum sample contains ten JSON-RPC payloads:

- **5 × `eth_getBlockByNumber`** responses with full transactions;
- **5 × `eth_getBlockReceipts`** responses for the same blocks.

The release Reth harness processes **2,908,507 raw bytes** in total.

## Why Ethereum data is relevant

Ethereum execution-layer payloads combine large amounts of structured hexadecimal and repeated metadata: transaction fields, addresses, topics, bloom filters, receipt fields and logs. This makes them a useful public test for a codec intended to reduce structured blockchain data.

Potential integration targets, each requiring its own validation, include:

- large JSON-RPC responses;
- receipt and log export pipelines;
- snapshot or archival sidecars;
- database/export representations;
- future RLP block or transaction batches;
- future networking experiments.

The current public benchmark is intentionally **outside the live Reth node runtime**. That lets compression behavior be measured before introducing a dependency into networking, storage or sync paths.

---

## Benchmark methodology

Standalone tool:

```text
tools/reth-zchain-bench/
```

Dataset fetch:

```bash
cd tools/reth-zchain-bench
./fetch-ethereum-payloads.sh benchmark-data/ethereum-json
```

Benchmark:

```bash
cargo run --release -- benchmark-data/ethereum-json benchmark-data/reth-zchain-results.csv
```

The benchmark records raw/compressed bytes, ratio, savings, timing, throughput, SHA-256 input/output and round-trip status.

### Measurement boundary

The Reth harness is a **release integration benchmark**. Native codec-only speed is measured separately by the C in-memory benchmark, where file I/O, process startup, CSV output and hashing are outside the timed section.

---

## Release Reth results

Observed on 2026-08-15:

| Payload | Files | Raw bytes | ZChain bytes | Savings | Encode | Decode | Round trip |
|---|---:|---:|---:|---:|---:|---:|---|
| Full block JSON | 5 | 1,256,590 | 363,968 | **71.04%** | 31.578 ms | 39.715 ms | **5/5 OK** |
| Receipts JSON | 5 | 1,651,917 | 195,266 | **88.18%** | 13.306 ms | 18.803 ms | **5/5 OK** |
| **Total** | **10** | **2,908,507** | **559,234** | **80.77%** | **44.884 ms** | **58.518 ms** | **10/10 OK** |

Derived effective throughput for this specific Reth release run:

- **~61.8 MiB/s encode**
- **~47.4 MiB/s decode**

These values describe this harness and dataset; they are not universal codec throughput claims.

---

## Native v4 and Speed_First results on Ethereum

The newer native C profile comparison uses the same Ethereum payload class inside the 64-file public blockchain corpus.

| Workload | Profile | Savings / size behavior | Encode | Decode |
|---|---|---|---:|---:|
| Ethereum block JSON | **v4** | **71.04% savings** | **71.63 MiB/s** | **42.56 MiB/s** |
| Ethereum block JSON | **Speed_First** | +2.59% compressed size vs v4 | **79.07 MiB/s** | **45.54 MiB/s** |
| Ethereum receipts JSON | **v4** | **88.18% savings** | **143.14 MiB/s** | **92.83 MiB/s** |
| Ethereum receipts JSON | **Speed_First** | +5.62% compressed size vs v4 | **162.55 MiB/s** | **101.13 MiB/s** |

### What the result means

**Receipts are one of the strongest ZChain workloads in the current public corpus.** The v4 compatibility profile keeps the tested v3 compressed output while reaching 143.14 MiB/s encode; Speed_First increases throughput further when a new bitstream can be deployed end-to-end.

Full block JSON is less compressible than receipts, but still shows **71.04% savings with v4** in the documented corpus.

---

## Practical advantages being evaluated

If validated in the relevant production path, this type of compression can target:

1. **Smaller RPC responses** for data-heavy services.
2. **Reduced archive/export size** for structured block and receipt data.
3. **Lower transfer cost** between execution-data systems, analytics and storage tiers.
4. **Potential future storage/network savings** where codec latency remains acceptable.

The benchmark does **not** claim that Reth P2P traffic or database size automatically shrinks by the same percentages.

---

## Integrity

The standalone Reth release benchmark reports **10/10 SHA-256 verified round trips** on the documented Ethereum payloads.

Lossless round-trip validation is a requirement for every public ZChain benchmark path.

---

## Next engineering steps

A deeper Reth evaluation should:

- pull canonical block bodies and receipts from a Reth database or RPC provider;
- compare JSON against RLP and Reth database codec representations;
- benchmark any storage hook independently;
- benchmark any networking hook independently;
- report p50/p95/p99 latency on representative execution-client hardware.

---

## Related pages

- [ZChain evolution: v3 → v4 → Speed_First](../README.md)
- [Selected codec comparison](COMPRESSION_COMPARISON.md)
- [Native C benchmark methodology](ZCHAIN_C_REAL_BENCH_REPORT.md)
- [Public claims and boundaries](PUBLIC_CLAIMS.md)
