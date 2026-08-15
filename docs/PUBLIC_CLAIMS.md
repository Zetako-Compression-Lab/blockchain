# ZChain Public Claims

This file defines what the current public evidence supports and what remains out of scope until stronger validation exists.

The repository currently contains two distinct evidence classes:

- **Reth / Ethereum:** release-mode standalone benchmark on real Ethereum JSON-RPC payloads.
- **Agave / Solana:** development integration prototype with fail-open shims, benchmark tooling, and a shred shadow probe.

These evidence classes must not be mixed when making performance claims.

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

The throughput values above are **dataset-specific derived effective throughput**, not universal codec guarantees.

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

## Agave performance boundary

**No public ZChain speed figure is claimed from the initial Agave development-build benchmarks.**

Those measurements were produced through non-release integration benchmark commands and may include harness, integration-layer, and development-build overhead. They are therefore **not representative evidence of ZChain codec throughput** and are intentionally excluded from performance claims.

Agave-specific speed should be published only after:

- `--release` execution;
- an in-memory benchmark path that isolates codec cost;
- representative validator hardware;
- clear separation of codec time from integration/harness overhead.

The release-mode Reth benchmark demonstrates materially higher throughput on a different blockchain workload, but it should **not** be used to infer Agave throughput directly.

## Claims to avoid for now

- "Production-ready Agave integration."
- "Production-ready Reth node integration."
- "Production validator throughput."
- "Mainnet bandwidth reduction of X%."
- "Shred traffic savings of X%" until representative shred-shadow datasets are collected.
- Any encode/decode MiB/s value derived from the initial Agave development-build run.
- "Reth networking is 80.77% smaller" — the measured dataset is JSON-RPC payloads, not the Reth P2P wire path.
- "Universal ZChain speed is 61.8/47.4 MiB/s" — those are dataset-specific effective figures from the documented Reth release run.
- "Wire-format compatible compressed transport" because the current Agave shadow path does not change the wire format.
- "Zero-risk integration" or similar absolute reliability wording.

## Current technical caveats

The current Agave port still emits:

- `unused_imports`
- `static_mut_refs`

They do not block the documented benchmark commands, but they should be cleaned before describing the integration as production-ready.

## Public wording candidates

- **ZChain achieved 80.77% aggregate savings across ten real Ethereum JSON-RPC block and receipt payloads in a release-mode Reth benchmark, with 10/10 SHA-256 verified round trips.**
- **The documented Reth release run corresponds to approximately 61.8 MiB/s effective encode throughput and 47.4 MiB/s effective decode throughput for that specific dataset.**
- **ZChain has a validated Agave development integration with fail-open compression shims, reproducible round-trip tooling, and an optional shred shadow probe.**
- **Initial Agave development-build timing is excluded from ZChain speed claims because it does not isolate release-mode codec performance.**
- **The Agave shred shadow probe is designed to measure compression behavior without changing the network wire format.**

## Required before stronger claims

### Agave

- clean current Rust warnings;
- benchmark with `--release`;
- isolate codec cost in memory;
- benchmark on representative validator hardware;
- collect representative shred-shadow datasets;
- publish latency/throughput distributions rather than selected examples;
- validate any future compressed write or transport path separately.

### Reth

- add native Reth database/RLP payload sources;
- compare JSON vs RLP vs database codecs;
- benchmark any future storage/network hook independently;
- measure distributions on representative execution-client hardware before node-runtime performance claims.