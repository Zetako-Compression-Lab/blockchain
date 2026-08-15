# ZChain Public Claims

This file defines what the current public evidence supports and what remains out of scope.

## Evidence classes

The public repository now separates four evidence layers:

1. **Native C profile comparison** — v3, v4, and Speed_First measured in-memory on the same public blockchain corpus.
2. **Reth / Ethereum** — release-mode Rust benchmark on real Ethereum JSON-RPC payloads.
3. **Solana / CometBFT payload corpora** — real public-chain JSON-RPC/RPC data exercised by the C harness.
4. **Agave integration** — development integration prototype with fail-open shims and shred shadow measurement.

These layers must not be mixed when making claims.

---

## Safe claims — v3 / v4 / Speed_First

Test system: Apple M4, macOS Darwin 25.5.0 arm64. Scope: C codec only, in memory; file loading, process startup, CSV writing, and hashing are outside the timed section.

Full public corpus: **64 files / 30,017,805 raw bytes**.

| Claim | Supported result |
|---|---|
| v3 aggregate savings | **82.14%** |
| v3 release throughput | **74.27 MiB/s encode / 67.96 MiB/s decode** |
| v4 aggregate savings | **82.14%** |
| v4 release throughput | **113.36 MiB/s encode / 68.54 MiB/s decode** |
| v4 vs v3 encode speedup | **1.53×** |
| v4 compressed output vs v3 | **Same compressed bytes on this corpus** |
| Speed_First aggregate savings | **81.82%** |
| Speed_First release throughput | **126.53 MiB/s encode / 74.67 MiB/s decode** |
| Speed_First vs v4 encode speedup | **1.12×** |
| Speed_First vs v4 decode speedup | **1.09×** |
| Speed_First compressed-size delta vs v4 | **+1.79% aggregate** |
| Speed_First bitstream compatibility | **Not compatible with v3/v4; encoder and decoder must deploy together** |

### Public wording

- **ZChain v4 preserves the v3 compressed byte counts on the tested 64-file blockchain corpus while increasing release encode throughput from 74.27 to 113.36 MiB/s.**
- **ZChain Speed_First reaches 126.53 MiB/s encode and 74.67 MiB/s decode on the same corpus, with a 1.79% aggregate compressed-size increase versus v4.**
- **Speed_First is a new high-throughput profile, not a drop-in bitstream-compatible replacement for v3/v4.**

Do not generalize “same bytes” beyond the tested corpus unless separately verified.

---

## Safe claims — representative workloads

### Ethereum / Reth payloads

| Workload | Profile | Savings / size behavior | Encode MiB/s | Decode MiB/s |
|---|---|---:|---:|---:|
| Ethereum block JSON | v4 | 71.04% savings | 71.63 | 42.56 |
| Ethereum block JSON | Speed_First | +2.59% size vs v4 | 79.07 | 45.54 |
| Ethereum receipts JSON | v4 | 88.18% savings | 143.14 | 92.83 |
| Ethereum receipts JSON | Speed_First | +5.62% size vs v4 | 162.55 | 101.13 |

The standalone Reth release harness additionally reports **10/10 SHA-256 round trips OK** and **80.77% aggregate savings** on its ten real Ethereum JSON-RPC payloads.

### Solana mainnet RPC

The public Solana corpus contains real `getBlock`, signatures-only block, commitment, production, and transaction responses.

- Historical v3 C release sample: **82.79% aggregate savings** across 21 files / 26,106,696 bytes.
- Full `getBlock` JSON: **84.18% savings**.
- v4 full-block throughput: **124.35 MiB/s encode / 74.99 MiB/s decode**.
- Speed_First full-block throughput: **138.55 MiB/s encode / 81.98 MiB/s decode**.

### CometBFT / Cosmos Hub

The public corpus includes `block`, `block_results`, `commit`, `validators`, and `consensus_params` payloads.

- CometBFT `block_results` v4: **84.87% savings, 120.74 MiB/s encode, 77.23 MiB/s decode**.
- Speed_First on the same workload: **143.42 MiB/s encode, 85.77 MiB/s decode**, with +1.03% size vs v4.

---

## Profile positioning

### v3

Use as the historical compatibility baseline and reference stream profile.

### v4

Use when backward compatibility matters. On the tested corpus, v4 keeps the same compressed bytes as v3 while substantially increasing encode throughput.

### Speed_First

Use when both endpoints can deploy a new profile together and throughput is the primary constraint. It is lossless but not bitstream-compatible with v3/v4.

The aggregate size cost is small on the current corpus, but the effect varies by workload. Some payload groups are slightly smaller than v4, while others are larger.

---

## Agave performance boundary

The initial Agave development-build throughput figures are **not** ZChain codec speed claims.

They were produced by a non-release integration harness and may include development-build, adapter, buffer, hashing, and harness overhead. They remain excluded from public codec-performance positioning.

The Agave integration is valid public evidence for:

- feature-gated build integration;
- fail-open behavior;
- round-trip benchmark tooling;
- shred shadow measurement without changing the wire format.

Agave-native hot-path performance requires its own release-mode, in-memory measurements on representative validator hardware.

---

## Claims to avoid

- “Universal ZChain speed is 126.53 MiB/s.” The figure is corpus/hardware/build specific.
- “v4 is byte-for-byte compatible with v3 on every possible payload.” Current evidence supports this only on the tested corpus.
- “Speed_First is compatible with v3/v4 streams.” It is not.
- “Speed_First always compresses worse.” The aggregate size is larger, but several payload groups are equal or smaller.
- “Production-ready Agave/Reth integration.” Current work is benchmark/integration evidence, not a production deployment claim.
- “Mainnet bandwidth will be reduced by X%.” RPC payload compression is not equivalent to network-wire savings.
- “Shred traffic savings are X%.” Real shred-shadow datasets still need to be collected and published.

---

## Next validation priorities

- representative x86_64 server hardware;
- release-mode Agave in-memory/hot-path benchmarking;
- larger real shred-shadow corpus;
- native Reth RLP/database payloads;
- broader CometBFT and Solana distributions;
- cycles/byte on platforms with a stable counter;
- compatibility regression corpus for v4;
- Speed_First framing/version-negotiation documentation for production integration.