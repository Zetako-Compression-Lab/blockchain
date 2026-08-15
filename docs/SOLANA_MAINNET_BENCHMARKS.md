# Solana Mainnet — ZChain Benchmark Page

[← Documentation index](README.md) · [Product evolution](../README.md) · [Agave integration](AGAVE_INTEGRATION.md)

This page documents ZChain on **real Solana mainnet JSON-RPC payloads**. It is intentionally separate from the Agave validator-integration page: this page measures codec behavior on Solana data; the Agave page covers validator-facing integration architecture.

## What we compress

The public Solana dataset contains:

- `getBlock` with **full transaction details**;
- `getBlock` with **signatures only**;
- `getBlockCommitment`;
- `getBlockProduction`;
- sampled `getTransaction` responses.

Historical v3 C release corpus:

- **21 files**
- **26,106,696 raw bytes**
- **4,493,986 compressed bytes**
- **82.79% aggregate savings**

## Why Solana data is relevant

Solana RPC can return very large, highly structured block and transaction responses. Full block JSON combines account keys, signatures, instructions, metadata, balances, logs and other repeated structural fields.

Potential benefits being evaluated include:

- reducing large RPC response sizes;
- lowering bandwidth for block-data services and indexers;
- reducing export/archive transfer size;
- evaluating compression before any validator-side storage or network integration.

A codec benchmark does not automatically translate into equivalent validator network savings; those require a separate integration test.

---

## Dataset methodology

The public fetcher collects finalized Solana mainnet RPC payloads using a configurable `SOLANA_RPC_URL`.

Representative fetch path:

```bash
cd agave/tools/solana-zchain-bench
./fetch-solana-mainnet-payloads.sh ../../../solana-zchain-data/mainnet-rpc 5 10
```

Native benchmark path:

```bash
cd zchain-c-bench
make clean && make
./zchain_c_bench_release 30 ../solana-zchain-data/mainnet-rpc
```

The C harness reports compressed size, savings, encode/decode throughput, ns/byte, latency distribution and round-trip status.

---

## Historical v3 release results

| Dataset | Files | Input bytes | ZChain bytes | Savings | Encode | Decode |
|---|---:|---:|---:|---:|---:|---:|
| **All Solana RPC** | 21 | 26,106,696 | 4,493,986 | **82.79%** | 73.44 MiB/s | 11.19 MiB/s |
| `getBlock` full JSON | 5 | 25,484,748 | 4,032,707 | **84.18%** | 78.13 MiB/s | 10.88 MiB/s |
| signatures-only | 5 | 591,574 | 441,299 | 25.40% | 21.32 MiB/s | 15.07 MiB/s |
| `getBlockCommitment` | 5 | 820 | 560 | 31.71% | 4.43 MiB/s | 3.04 MiB/s |
| `getBlockProduction` | 1 | 17,501 | 12,516 | 28.48% | 22.29 MiB/s | 15.22 MiB/s |
| `getTransaction` | 5 | 12,053 | 6,904 | 42.72% | 21.18 MiB/s | 12.24 MiB/s |

These v3 results are retained as part of the public evolution history.

---

## v4 and Speed_First

The current 64-file corpus demonstrates the newer profile improvements on the same payload classes.

| Solana workload | v4 encode | v4 decode | v4 savings / behavior | Speed_First encode | Speed_First decode | Size delta vs v4 |
|---|---:|---:|---|---:|---:|---:|
| `getBlock` full JSON | **124.35** | **74.99** | **84.18% savings** | **138.55** | **81.98** | +1.78% |
| signatures JSON | 36.53 | 21.30 | tested v3-compatible output | 40.66 | 23.01 | +0.97% |
| `getBlockCommitment` | 48.91 | 42.10 | tested v3-compatible output | 56.15 | 37.69 | +0.00% |
| `getBlockProduction` | 38.20 | 22.55 | tested v3-compatible output | 42.37 | 24.34 | **-1.41%** |
| `getTransaction` | 46.46 | 35.77 | tested v3-compatible output | 53.65 | 31.59 | **-0.07%** |

All throughput values are MiB/s from the documented Apple M4 native release benchmark.

### Strongest Solana result

The largest and most relevant payload class in this corpus is **full block JSON**:

- v4: **84.18% savings**, **124.35 MiB/s encode**, **74.99 MiB/s decode**;
- Speed_First: **138.55 MiB/s encode**, **81.98 MiB/s decode**, with +1.78% compressed size vs v4.

This is the clearest public Solana use case because it combines a large real payload with strong compression and high release throughput.

---

## Native codec vs Agave integration

These Solana RPC numbers are **native C codec measurements on Solana payloads**. They are not the same thing as the Agave integration harness.

The Agave prototype includes fail-open shims, benchmark tooling and a shred shadow path. Its original development-build timing is excluded from native ZChain speed claims because that run did not isolate optimized codec cost.

[Read the Agave integration page](AGAVE_INTEGRATION.md)

---

## What is not claimed

The current Solana RPC benchmark does not by itself prove:

- validator TPU/TVU bandwidth savings;
- shred wire-format compatibility;
- blockstore storage savings;
- mainnet validator latency impact;
- production Agave throughput.

Those require dedicated integration-level measurements.

---

## Related pages

- [Agave integration](AGAVE_INTEGRATION.md)
- [v3 → v4 → Speed_First evolution](../README.md)
- [Selected codec comparison](COMPRESSION_COMPARISON.md)
- [Public claims](PUBLIC_CLAIMS.md)
