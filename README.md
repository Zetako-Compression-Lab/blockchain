# ZChain

**Lossless compression for blockchain data paths.**

ZChain is Zetako's proprietary compression technology for structured blockchain and infrastructure payloads. This repository is the **public engineering showcase**: product evolution, reproducible benchmark methodology, blockchain-specific evaluations, and externally reviewable performance results live here. The proprietary codec core and protected production releases remain private.

> **What ZChain is trying to solve:** blockchain systems move and store large volumes of repetitive structured data — blocks, receipts, transaction metadata, RPC responses, validator artifacts, consensus results and logs. ZChain aims to reduce those bytes while keeping the data lossless and the processing cost low enough for practical infrastructure use.

---

## ZChain evolution

The public benchmark history is intentionally visible because the codec is under continuous optimization.

| Generation | Positioning | Savings on 64-file public corpus | Encode | Decode | Main delta |
|---|---|---:|---:|---:|---|
| **v3** | Compatibility baseline | **82.14%** | 74.27 MiB/s | 67.96 MiB/s | Historical reference |
| **v4** | Compatibility optimized | **82.14%** | **113.36 MiB/s** | **68.54 MiB/s** | **1.53× faster encode vs v3**, same compressed byte count on tested corpus |
| **Speed_First** | Throughput oriented | **81.82%** | **126.53 MiB/s** | **74.67 MiB/s** | **1.12× faster encode vs v4**, new bitstream, +1.79% aggregate compressed size |

**Benchmark environment:** Apple M4 · macOS Darwin 25.5.0 arm64 · in-memory C release benchmark · `-O3 -DNDEBUG -march=native` · **64 files / 30,017,805 raw bytes**. File loading, process startup, CSV output and hashing are outside the timed section.

![ZChain profile evolution](assets/zchain-v3-v4-speed-first.svg)

### What changed from v3 to v4

v4 is the current compatibility-oriented profile. On the tested public corpus it keeps the **same compressed byte counts as v3**, while increasing release encode throughput from **74.27 to 113.36 MiB/s**. Decode remains effectively stable.

### What Speed_First changes

Speed_First is a separate high-throughput profile for deployments where encoder and decoder can upgrade together. It reaches **126.53 MiB/s encode** and **74.67 MiB/s decode** on the same corpus. The aggregate compressed output is **1.79% larger than v4**, although the delta varies by workload and is negative on some payload groups.

### Which profile fits which deployment?

| Requirement | Profile |
|---|---|
| Existing v3 stream compatibility matters | **v4** |
| Preserve tested v3 output while increasing encode speed | **v4** |
| Maximum throughput and both endpoints can upgrade together | **Speed_First** |
| Historical/reference testing | **v3** |

[Full v3 → v4 → Speed_First report](docs/ZCHAIN_V3_V4_SPEED_FIRST_C_REAL_BENCH_REPORT.md) · [v3 vs v4 compatibility report](docs/ZCHAIN_V3_VS_V4_C_REAL_BENCH_REPORT.md)

---

## Blockchain benchmark pages

Each ecosystem has a dedicated page explaining **what is compressed, why it matters, how the dataset is collected, the benchmark methodology, measured results, and the integration boundary**.

| Ecosystem | What we benchmark | Why it matters | Page |
|---|---|---|---|
| **Ethereum / Reth** | full block JSON, receipts | RPC payload size, execution-layer data, future RLP/storage/network experiments | [Ethereum / Reth](docs/zchain-reth-benchmarks.md) |
| **Solana mainnet** | `getBlock`, signatures, commitments, production, transactions | large validator/RPC payloads and block data | [Solana](docs/SOLANA_MAINNET_BENCHMARKS.md) |
| **Agave** | integration shim, benchmark path, shred shadow probe | validator-side integration and safe measurement before changing wire/storage paths | [Agave integration](docs/AGAVE_INTEGRATION.md) |
| **Cosmos / CometBFT** | blocks, block results, commits, validators, consensus params | consensus and application metadata with multiple structured payload types | [CometBFT / Cosmos](docs/COMETBFT_COSMOS_BENCHMARKS.md) |

[Documentation index](docs/README.md)

---

## Current representative results

| Workload | Profile | Savings | Encode | Decode |
|---|---|---:|---:|---:|
| Ethereum receipts JSON | v4 | **88.18%** | **143.14 MiB/s** | **92.83 MiB/s** |
| Ethereum receipts JSON | Speed_First | workload-dependent vs v4 | **162.55 MiB/s** | **101.13 MiB/s** |
| Solana `getBlock` full JSON | v4 | **84.18%** | **124.35 MiB/s** | **74.99 MiB/s** |
| Solana `getBlock` full JSON | Speed_First | +1.78% size vs v4 | **138.55 MiB/s** | **81.98 MiB/s** |
| CometBFT `block_results` JSON | v4 | **84.87%** | **120.74 MiB/s** | **77.23 MiB/s** |
| CometBFT `block_results` JSON | Speed_First | +1.03% size vs v4 | **143.42 MiB/s** | **85.77 MiB/s** |

These are **workload-, hardware-, build- and profile-specific measurements**, not universal throughput guarantees.

---

## Why compression is useful in blockchain infrastructure

Depending on where it is integrated and separately validated, reducing structured blockchain payloads can target four practical costs:

1. **Network transfer** — fewer bytes for RPC responses, replication, exports or future protocol-specific transport paths.
2. **Storage** — smaller block, receipt, log, snapshot or archival representations where compression is appropriate.
3. **Infrastructure cost** — less bandwidth and storage pressure for nodes, indexers, APIs and data services.
4. **Operational mobility** — faster movement of large structured datasets between systems, backups, analytics and archive tiers when the codec overhead is justified.

ZChain does **not** claim that a benchmark saving automatically becomes an equivalent mainnet bandwidth or storage saving. Every production path needs its own integration benchmark.

---

## Competitive benchmark page

A separate page compares ZChain against selected established codecs on the **same payloads and stated presets**. The comparison currently includes **gzip, Brotli and LZMA2/7zip**.

Highlights from the documented workloads include:

- Ethereum receipts 1 MiB: ZChain Speed_First produces **43,318 B at 170 MiB/s encode**, versus gzip-6 at 61,489 B / 185 MiB/s and Brotli q5 at 42,279 B / 159 MiB/s.
- `blocks-4m`: ZChain Speed_First produces **165 KB at 170 MiB/s**, versus gzip-6 at 334 KB / 105 MiB/s, Brotli q5 at 253 KB / 128 MiB/s and LZMA2 p1 at 227 KB / 67 MiB/s.

[Open the codec comparison](docs/COMPRESSION_COMPARISON.md)

---

## Benchmark policy

ZChain separates **native codec measurements** from **integration-harness measurements**.

| Evidence | Meaning |
|---|---|
| Native C release benchmark | Best evidence for codec-only speed on the documented hardware/corpus |
| Reth release harness | Scoped Ethereum integration benchmark |
| Solana RPC C corpus | Native codec behavior on real Solana RPC payloads |
| Agave development harness | Integration proof only; original non-release timing is excluded from native speed claims |
| Agave shadow path | Safe measurement architecture before any wire-format change |

[Public claims and measurement boundaries](docs/PUBLIC_CLAIMS.md)

---

## Public vs private

**Published here:** methodology, benchmark reports, payload descriptions, compatibility behavior, integration architecture and claim boundaries.

**Kept private:** proprietary compression core source, protected production builds/releases, internal datasets/test infrastructure and customer-specific integrations.

For technical evaluation, partnership or licensing discussions: **contact@zetako.ai**

© Zetako. Proprietary technology. Public documentation in this repository does not grant rights to reproduce, reverse engineer or redistribute the ZChain implementation.