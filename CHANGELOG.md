# ZChain Public Engineering Changelog

This changelog records public benchmark and documentation milestones. It does not expose proprietary codec implementation details.

## 2026-08-15 — Speed_First public profile

- Added the Speed_First high-throughput profile to the public benchmark story.
- Measured **126.53 MiB/s encode / 74.67 MiB/s decode** on the 64-file public blockchain corpus.
- Measured **81.82% aggregate savings**.
- Documented the **+1.79% aggregate compressed-size delta vs v4** and the fact that the direction varies by workload.
- Documented that Speed_First uses a new bitstream/profile and must be deployed with a matching decoder.

## 2026-08-15 — v4 compatibility optimization

- Added ZChain v4 as the compatibility-oriented optimized generation.
- On the tested public corpus, v4 preserves the **same compressed byte counts as v3**.
- Increased encode throughput from **74.27 MiB/s to 113.36 MiB/s**, approximately **1.53×**.
- Decode remained effectively stable at **68.54 MiB/s**.

## 2026-08-15 — Expanded public blockchain corpus

- Expanded the public C benchmark corpus to **64 files / 30,017,805 raw bytes**.
- Added/organized evidence for:
  - Ethereum / Reth block and receipt payloads;
  - Solana mainnet RPC payloads;
  - Cosmos Hub / CometBFT RPC payloads;
  - Agave validator integration experiments.

## 2026-08-15 — Native C benchmark baseline

- Added an in-memory C benchmark harness separating codec time from file loading, process startup, CSV writing and hashing.
- Established release-mode methodology using `-O3 -DNDEBUG -march=native` on Apple M4.
- Added p50/p95/p99 and ns/byte reporting.

## 2026-08-15 — Reth / Ethereum benchmark

- Added a standalone release-mode Reth-adjacent benchmark.
- Measured **80.77% aggregate savings** across ten documented Ethereum JSON-RPC payloads.
- Verified **10/10 SHA-256 round trips**.

## 2026-08-15 — Agave integration prototype

- Added fail-open Agave-facing compression shims.
- Added single-file and directory benchmark tooling.
- Added optional shred shadow measurement without changing the wire format.
- Explicitly separated development-harness timing from native codec speed claims.

---

For the current technical view, start with [README.md](README.md). For blockchain-specific pages, see [docs/README.md](docs/README.md).
