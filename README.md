# ZChain

**Lossless compression for blockchain clients, validator infrastructure, and execution-layer data.**

ZChain is Zetako's proprietary compression technology for structured blockchain and infrastructure payloads. This repository is a **public technical showcase**: it publishes reproducible benchmark methodology, compatibility notes, integration experiments, and externally reviewable metrics while keeping the proprietary core source and protected releases private.

The current public benchmark corpus covers **64 files / 30,017,805 raw bytes** across:

- **Agave / Solana** integration and source payloads
- **Reth / Ethereum** block and receipt JSON-RPC payloads
- **CometBFT / Cosmos Hub** RPC payloads
- **Solana mainnet RPC** block, transaction, commitment, and production payloads

> **Current product direction:** ZChain v4 is the compatibility-oriented profile. `Speed_First` is the higher-throughput profile for deployments where both encoder and decoder can move to the new bitstream together.

---

## Headline benchmark

Apple M4 · macOS Darwin 25.5.0 arm64 · in-memory C benchmark · release build (`-O3 -DNDEBUG -march=native`). File loading, process startup, CSV output, and hashing are outside the timed section.

| Profile | Compressed bytes | Savings | Encode MiB/s | Decode MiB/s | Compatibility |
|---|---:|---:|---:|---:|---|
| **v3 Compatibility** | 5,361,692 | **82.14%** | 74.27 | 67.96 | v3 baseline |
| **v4 Compatibility** | 5,361,692 | **82.14%** | **113.36** | **68.54** | Same compressed bytes as v3 on this corpus |
| **Speed_First** | 5,457,493 | **81.82%** | **126.53** | **74.67** | New bitstream/profile |

### Evolution at a glance

- **v4 vs v3:** same compressed output size on the tested corpus, **1.53× faster encode**, effectively stable decode.
- **Speed_First vs v4:** **1.12× faster encode** and **1.09× faster decode**, with a **1.79% aggregate compressed-size increase**.
- **Speed_First vs v3:** **1.70× faster encode**.

![ZChain profile evolution](assets/zchain-v3-v4-speed-first.svg)

See [v3 vs v4 vs Speed_First](docs/ZCHAIN_V3_V4_SPEED_FIRST_C_REAL_BENCH_REPORT.md) for the complete comparison.

---

# ZChain v3 → v4 → Speed_First

## v3 — compatibility baseline

v3 is the historical compatibility profile. It establishes the original public bitstream and compression behavior used as the reference point for later optimization work.

On the full public corpus:

- **74.27 MiB/s encode**
- **67.96 MiB/s decode**
- **82.14% aggregate savings**

## v4 — faster without changing the tested output

v4 is the preferred profile when **backward compatibility matters**.

On the 64-file public corpus, v4 produces the **same compressed byte counts as v3** while increasing release encode throughput to **113.36 MiB/s**. Decode remains effectively stable at **68.54 MiB/s**.

This makes v4 the strongest public compatibility result: **same tested compression output, substantially faster encode**.

Representative v4 results:

| Workload | Savings | Encode MiB/s | Decode MiB/s |
|---|---:|---:|---:|
| Ethereum block JSON | 71.04% | 71.63 | 42.56 |
| Ethereum receipts JSON | 88.18% | 143.14 | 92.83 |
| CometBFT block results JSON | 84.87% | 120.74 | 77.23 |
| Solana `getBlock` full JSON | 84.18% | 124.35 | 74.99 |

## Speed_First — throughput-oriented profile

`Speed_First` is designed for cases where **speed is the first constraint** and both sides can deploy a new encoder/decoder profile together.

On the same public corpus:

- **126.53 MiB/s encode**
- **74.67 MiB/s decode**
- **81.82% aggregate savings**
- **+1.79% compressed size vs v4** across the full corpus

The size tradeoff varies by payload. It is not always worse: some CometBFT and Solana payload groups are slightly smaller with Speed_First than with v4.

Representative Speed_First results:

| Workload | Encode MiB/s | Decode MiB/s | Size delta vs v4 |
|---|---:|---:|---:|
| Ethereum receipts JSON | **162.55** | **101.13** | +5.62% |
| Solana `getBlock` full JSON | **138.55** | **81.98** | +1.78% |
| CometBFT block results JSON | **143.42** | **85.77** | +1.03% |
| Ethereum block JSON | 79.07 | 45.54 | +2.59% |
| CometBFT block JSON | 58.18 | 34.19 | **-1.32%** |
| Solana block production JSON | 42.37 | 24.34 | **-1.41%** |

### Which profile should an integrator choose?

| Requirement | Recommended profile |
|---|---|
| Existing v3 stream compatibility matters | **v4** |
| Same tested compressed output as v3, but faster encode | **v4** |
| Maximum throughput and both endpoints can upgrade together | **Speed_First** |
| Historical compatibility / reference testing | **v3** |

---

# Public blockchain workloads

## Solana / Agave

The public corpus includes real Solana mainnet JSON-RPC payloads:

- `getBlock` with full transactions
- `getBlock` signatures-only
- `getBlockCommitment`
- `getBlockProduction`
- sampled `getTransaction` responses

The earlier C release benchmark on 21 Solana mainnet RPC files measured **82.79% aggregate savings**, including **84.18% savings** on full block JSON. The newer v4/Speed_First corpus confirms high throughput on the same class of workload.

The Agave integration itself remains a **development integration prototype** with fail-open shims and a shred shadow probe. Initial non-release Agave harness speed figures are excluded from codec performance claims.

See [Solana mainnet benchmarks](docs/SOLANA_MAINNET_BENCHMARKS.md) and [Agave integration](docs/AGAVE_INTEGRATION.md).

## Ethereum / Reth

The public Ethereum dataset contains real JSON-RPC block and receipt payloads. The release Reth harness verifies **10/10 SHA-256 round trips** and **80.77% aggregate savings** on its documented sample.

The native C profile comparison shows particularly strong results on Ethereum receipts:

- v4: **143.14 MiB/s encode / 92.83 MiB/s decode**
- Speed_First: **162.55 MiB/s encode / 101.13 MiB/s decode**

See [Reth / Ethereum benchmarks](docs/zchain-reth-benchmarks.md).

## Cosmos / CometBFT

The public CometBFT dataset includes real Cosmos Hub RPC payloads for:

- `block`
- `block_results`
- `commit`
- `validators`
- `consensus_params`

`block_results` is a particularly strong workload in the current corpus: v4 reaches **120.74 MiB/s encode / 77.23 MiB/s decode** with **84.87% savings**; Speed_First reaches **143.42 / 85.77 MiB/s**.

See [CometBFT / Cosmos benchmarks](docs/COMETBFT_COSMOS_BENCHMARKS.md).

---

# Benchmark policy

ZChain publishes **native codec performance separately from integration-harness performance**.

| Evidence layer | What it measures | Use for codec speed claims? |
|---|---|---|
| Native C release benchmark | In-memory codec work | **Yes, with hardware/dataset scope** |
| Reth release harness | Release integration benchmark on Ethereum JSON-RPC payloads | Yes, as scoped Reth evidence |
| Agave development harness | Codec + integration + development overhead | **No** |
| Agave shadow/integration path | Integration safety and future shred measurement | Integration evidence, not native speed |

All headline v3/v4/Speed_First figures in this README come from the same in-memory C benchmark methodology on the same public blockchain corpus.

---

# Public vs private

**Public**

- benchmark methodology and reports
- reproducible payload-fetch instructions
- compatibility and profile behavior
- integration architecture
- public claim boundaries

**Private**

- proprietary compression core source
- protected production releases
- internal datasets and test infrastructure
- customer-specific integrations

---

# Documentation

- [v3 vs v4 vs Speed_First](docs/ZCHAIN_V3_V4_SPEED_FIRST_C_REAL_BENCH_REPORT.md)
- [v3 vs v4 compatibility comparison](docs/ZCHAIN_V3_VS_V4_C_REAL_BENCH_REPORT.md)
- [Native C benchmark methodology](docs/ZCHAIN_C_REAL_BENCH_REPORT.md)
- [Reth / Ethereum benchmarks](docs/zchain-reth-benchmarks.md)
- [Solana mainnet benchmarks](docs/SOLANA_MAINNET_BENCHMARKS.md)
- [CometBFT / Cosmos benchmarks](docs/COMETBFT_COSMOS_BENCHMARKS.md)
- [Agave integration](docs/AGAVE_INTEGRATION.md)
- [Public claims](docs/PUBLIC_CLAIMS.md)

For technical evaluation, partnership, or licensing discussions: **contact@zetako.ai**

© Zetako. Proprietary technology. Public documentation in this repository does not grant rights to reproduce, reverse engineer, or redistribute the ZChain implementation.