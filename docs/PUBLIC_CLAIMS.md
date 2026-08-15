# ZChain Public Claims

This file defines what the current public evidence supports and what remains out of scope until stronger validation exists.

The repository contains three distinct evidence classes:

- **Native C release baseline:** isolated in-memory codec benchmark on Apple M4 using `-O3 -DNDEBUG -march=native`.
- **Reth / Ethereum:** standalone `cargo run --release` benchmark on real Ethereum JSON-RPC payloads.
- **Agave / Solana:** integration prototype with fail-open shims, benchmark tooling, and a shred shadow probe.

These evidence classes must remain separate when making performance claims.

## Safe claims — native C release baseline

| Claim | Status | Scope |
|---|---|---|
| Release codec benchmark | Verified | Standalone C harness, `-O3 -DNDEBUG -march=native`, Apple M4 |
| Dataset size | Measured | 18 files, 2,942,777 raw bytes |
| Aggregate savings | Measured | 2,942,777 B → 572,658 B, 80.54% savings |
| Aggregate encode throughput | Measured | **63.15 MiB/s** |
| Aggregate decode throughput | Measured | **11.19 MiB/s** |
| Aggregate encode cost | Measured | 15.10 ns/byte |
| Aggregate decode cost | Measured | 85.19 ns/byte |
| Ethereum block JSON | Measured | 71.04% savings, 44.19 MiB/s encode, 11.66 MiB/s decode |
| Ethereum receipts JSON | Measured | 88.18% savings, 96.84 MiB/s encode, 10.38 MiB/s decode |
| Latency distributions | Measured | p50/p95/p99 reported in `ZCHAIN_C_REAL_BENCH_REPORT.md` |

The native C baseline is the current reference for **codec speed** because its timed section excludes process startup, file reads, CSV writes, SHA hashing, and higher-level integration overhead.

The measured performance above is **hardware-, dataset-, build-, and implementation-specific**, not a universal throughput guarantee.

## Safe claims — Reth / Ethereum

| Claim | Status | Scope |
|---|---|---|
| Release-mode benchmark execution | Verified | `cargo run --release` in the standalone Reth benchmark harness |
| Real Ethereum JSON-RPC payload set | Verified in documented experiment | Five full block responses plus five matching receipts responses |
| SHA-256 round-trip verification | Verified | 10/10 documented payloads report successful round trip |
| Full block JSON savings | Measured | 71.04% across the five documented `eth_getBlockByNumber` payloads |
| Receipts JSON savings | Measured | 88.18% across the five documented `eth_getBlockReceipts` payloads |
| Aggregate savings | Measured | 2,908,507 B → 559,234 B, 80.77% savings |
| Aggregate encode time | Measured | 44.884 ms |
| Aggregate decode time | Measured | 58.518 ms |
| Effective encode throughput | Calculated from measured aggregate | ~61.8 MiB/s for this dataset/run |
| Effective decode throughput | Calculated from measured aggregate | ~47.4 MiB/s for this dataset/run |

The Reth throughput values are **dataset-specific effective integration-harness throughput**, not the native codec baseline and not universal ZChain guarantees.

## Safe claims — Agave / Solana

| Claim | Status | Scope |
|---|---|---|
| Agave-facing ZChain v3 integration builds | Verified | `solana-ledger`, `zchain-bench`, and `zchain-bench-dir` pass documented `cargo check` commands with `zchainv3` |
| Single-file benchmark tooling | Verified | `tools/zchain-bench` |
| Directory CSV benchmark tooling | Verified | `tools/zchain-bench-dir` |
| SHA-256 round-trip verification | Verified in documented benchmark runs | Reported by benchmark path |
| Fail-open behavior | Verified by integration design | Compression errors return original bytes instead of failing validator paths |
| Shred shadow measurement path | Implemented as optional probe | Measures shred payload compression without changing the wire format |
| Single-file savings | Measured locally | 6,000 B → 2,887 B, 51.88% savings, SHA-256 OK |
| Directory sample savings | Measured locally | 49.07% to 72.03% across seven listed Rust source files |
| Directory sample round trip | Verified in documented rows | All seven listed rows report OK |
| Cargo release sanity check | Measured | 51.88% savings, 7.63 MiB/s encode, 5.88 MiB/s decode, round trip OK on the 6,000-byte benchmark report |

## Agave performance boundary

**The initial Agave development-build throughput figures are not ZChain codec speed claims.**

They came from a non-release integration harness and may include development-build, adapter, buffer, hashing, and harness overhead. They are intentionally excluded from the public codec-speed baseline.

The native C release benchmark independently demonstrates materially higher in-memory codec throughput. This shows why native codec performance and Agave integration-level performance must be reported separately.

The Agave Cargo `--release` sanity check is valid as a scoped **integration-level** measurement, but it is not used as the native codec baseline.

## Claims to avoid for now

- "Production-ready Agave integration."
- "Production-ready Reth node integration."
- "Production validator throughput."
- "Mainnet bandwidth reduction of X%."
- "Shred traffic savings of X%" until representative shred-shadow datasets are collected.
- Any MiB/s value from the original Agave development-build run as a ZChain codec claim.
- "Reth networking is 80.77% smaller" — the measured dataset is JSON-RPC payloads, not the Reth P2P wire path.
- "Universal ZChain speed is 63.15 MiB/s" — 63.15 MiB/s is the measured aggregate encode throughput of the documented native C release corpus on Apple M4.
- "Universal decode speed is 11.19 MiB/s" — the value is dataset/hardware/build specific.
- "Wire-format compatible compressed transport" because the current Agave shadow path does not change the wire format.
- "Zero-risk integration" or similar absolute reliability wording.

## Current technical caveats

The current Agave port still emits:

- `unused_imports`
- `static_mut_refs`

They do not block the documented benchmark commands, but they should be cleaned before describing the integration as production-ready.

The native C benchmark also exposes a clear performance asymmetry: **decode is materially slower than encode** in the current implementation. Decode optimization is therefore a priority before any hot-path production claim.

## Public wording candidates

- **In an isolated Apple M4 C release benchmark, ZChain measured 63.15 MiB/s aggregate encode throughput with 80.54% savings across an 18-file, 2.94 MB corpus.**
- **On the Ethereum receipts subset, ZChain measured 96.84 MiB/s encode throughput with 88.18% savings.**
- **On Ethereum full block JSON, ZChain measured 44.19 MiB/s encode throughput with 71.04% savings.**
- **ZChain achieved 80.77% aggregate savings across ten real Ethereum JSON-RPC block and receipt payloads in a release-mode Reth benchmark, with 10/10 SHA-256 verified round trips.**
- **ZChain has a validated Agave development integration with fail-open compression shims, reproducible round-trip tooling, and an optional shred shadow probe.**
- **Initial Agave development-build timing is excluded from ZChain codec speed claims because it does not isolate release-mode codec performance.**

## Required before stronger claims

### Native codec

- profile and optimize decode;
- run the same harness on representative x86_64 server CPUs;
- add stable hardware-cycle measurements where available;
- expand corpus diversity and publish distributions.

### Agave

- clean current Rust warnings;
- isolate codec cost in memory inside the Agave integration;
- benchmark on representative validator hardware;
- collect representative shred-shadow datasets;
- publish latency/throughput distributions rather than selected examples;
- validate any future compressed write or transport path separately.

### Reth

- add native Reth database/RLP payload sources;
- compare JSON vs RLP vs database codecs;
- benchmark any future storage/network hook independently;
- measure distributions on representative execution-client hardware before node-runtime performance claims.