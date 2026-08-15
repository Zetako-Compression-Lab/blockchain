# ZChain Benchmarks for Solana Mainnet RPC Payloads

This document describes the public Solana mainnet JSON-RPC payload corpus used by the ZChain C benchmark harness.

## Dataset

The fetcher collects:

- `getBlock` with full transaction details
- `getBlock` with signatures only
- `getBlockCommitment`
- `getBlockProduction`
- `getTransaction` for sampled signatures

Historical v3 C release corpus:

- **21 files**
- **26,106,696 raw bytes**
- **4,493,986 ZChain bytes**
- **82.79% aggregate savings**

## v3 C release results

| Dataset | Files | Input bytes | ZChain bytes | Savings | Encode MiB/s | Decode MiB/s |
|---|---:|---:|---:|---:|---:|---:|
| all | 21 | 26,106,696 | 4,493,986 | **82.79%** | 73.44 | 11.19 |
| `getBlock` full JSON | 5 | 25,484,748 | 4,032,707 | **84.18%** | 78.13 | 10.88 |
| `getBlock` signatures JSON | 5 | 591,574 | 441,299 | 25.40% | 21.32 | 15.07 |
| `getBlockCommitment` JSON | 5 | 820 | 560 | 31.71% | 4.43 | 3.04 |
| `getBlockProduction` JSON | 1 | 17,501 | 12,516 | 28.48% | 22.29 | 15.22 |
| `getTransaction` JSON | 5 | 12,053 | 6,904 | 42.72% | 21.18 | 12.24 |

## Current profile comparison

On the expanded 64-file public blockchain corpus, the newer profiles improve throughput substantially.

| Workload | Profile | Encode MiB/s | Decode MiB/s | Size behavior |
|---|---|---:|---:|---|
| Solana `getBlock` full JSON | v4 | **124.35** | **74.99** | 84.18% savings; same v3 bytes on tested corpus |
| Solana `getBlock` full JSON | Speed_First | **138.55** | **81.98** | +1.78% compressed size vs v4 |
| Solana signatures JSON | v4 | 36.53 | 21.30 | v3-compatible output on tested corpus |
| Solana signatures JSON | Speed_First | 40.66 | 23.01 | +0.97% vs v4 |
| Solana block production JSON | v4 | 38.20 | 22.55 | v3-compatible output on tested corpus |
| Solana block production JSON | Speed_First | 42.37 | 24.34 | **-1.41% size vs v4** |
| Solana transaction JSON | v4 | 46.46 | 35.77 | v3-compatible output on tested corpus |
| Solana transaction JSON | Speed_First | 53.65 | 31.59 | **-0.07% size vs v4** |

## Agave integration boundary

These RPC payload benchmarks measure native codec behavior on Solana data. They are separate from the Agave integration harness.

The Agave prototype provides fail-open shims, round-trip tooling, and a shred shadow probe. Its original development-build throughput figures are not used as native ZChain speed claims.

## Public interpretation

The strongest Solana public result is the full-block JSON workload: **84.18% measured savings**, **124.35 MiB/s encode with v4**, and **138.55 MiB/s encode with Speed_First** on the documented Apple M4 release benchmark environment.

These results are workload-, hardware-, and build-specific.