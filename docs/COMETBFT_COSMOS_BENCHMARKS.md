# Cosmos Hub / CometBFT — ZChain Benchmark Page

[← Documentation index](README.md) · [Product evolution](../README.md) · [Codec comparison](COMPRESSION_COMPARISON.md)

This page documents ZChain on **real CometBFT / Cosmos Hub RPC payloads**.

## What we compress

For selected Cosmos Hub heights, the public fetcher collects:

- `block`;
- `block_results`;
- `commit`;
- `validators`;
- `consensus_params`.

These payloads intentionally cover several different structured data shapes instead of one homogeneous JSON document.

## Why CometBFT data is relevant

CometBFT exposes consensus, validator and application-result data with repeated field structures, signatures, addresses and nested result objects. Compression can be useful for RPC delivery, exports, archives and data movement if the CPU/latency cost is justified.

The benchmark asks two practical questions:

1. How much can the byte volume of these payload classes be reduced?
2. How does the compatibility-oriented v4 profile compare with the higher-throughput Speed_First profile?

---

## Dataset methodology

Fetcher:

```bash
cd tools/zchain-bench
./fetch-cosmoshub-payloads.sh benchmark-data/cosmoshub-rpc 5
```

A different endpoint can be supplied with `COMETBFT_RPC_URL`.

Native C benchmark:

```bash
cd zchain-c-bench
make clean && make
./zchain_c_bench_release 100 ../cometbft-zchain-data/cosmoshub-rpc
```

The harness reports compressed size, savings, encode/decode throughput, ns/byte, latency distribution and round-trip status.

---

## v4 and Speed_First results

| Workload | v4 savings | v4 encode | v4 decode | Speed_First encode | Speed_First decode | Size delta vs v4 |
|---|---:|---:|---:|---:|---:|---:|
| `block` JSON | 52.66% | 49.72 | 31.47 | 58.18 | 34.19 | **-1.32%** |
| `block_results` JSON | **84.87%** | **120.74** | **77.23** | **143.42** | **85.77** | +1.03% |
| `commit` JSON | 52.75% | 52.20 | 31.40 | 57.64 | 34.05 | **-1.12%** |
| `consensus_params` JSON | 28.50% | 39.58 | 29.95 | 44.81 | 25.94 | +2.10% |
| `validators` JSON | 56.90% | 56.07 | 39.64 | 63.34 | 35.16 | +1.02% |

Throughput values are MiB/s from the documented Apple M4 native release benchmark.

## Strongest current Cosmos result

`block_results` is the most compelling workload in the public sample:

- **v4:** 84.87% savings, 120.74 MiB/s encode, 77.23 MiB/s decode;
- **Speed_First:** 143.42 MiB/s encode, 85.77 MiB/s decode, with only +1.03% compressed size vs v4.

This payload class combines strong savings with high throughput and therefore provides a useful public example of ZChain on consensus/application-result data.

## Interesting Speed_First behavior

Speed_First does not always make the output larger. In this corpus:

- `block` is **1.32% smaller** than v4;
- `commit` is **1.12% smaller** than v4.

The profile should therefore be described as having a **small aggregate size tradeoff whose direction varies by payload**, rather than as universally reducing compression ratio.

---

## What advantages are being evaluated

If separately validated in a production path, the observed behavior could be useful for:

- smaller RPC responses;
- cheaper transfer of block/result data to indexers and analytics systems;
- reduced archive/export volume;
- lower storage/transfer pressure for selected structured datasets.

These measurements do **not** claim that live CometBFT P2P traffic or node storage will shrink by the same percentages.

---

## Integration boundary

This is currently a **payload benchmark**, not a live CometBFT networking or database integration.

Before claiming production network/storage impact, a separate experiment should identify the exact payload representation and measure:

- encode/decode latency in the target path;
- CPU cost under node load;
- compatibility/failure behavior;
- p50/p95/p99 latency;
- net bandwidth or storage savings after framing/metadata overhead.

---

## Related pages

- [ZChain evolution: v3 → v4 → Speed_First](../README.md)
- [Selected codec comparison](COMPRESSION_COMPARISON.md)
- [Native benchmark methodology](ZCHAIN_C_REAL_BENCH_REPORT.md)
- [Public claims](PUBLIC_CLAIMS.md)
