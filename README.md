# ZChain

**A blockchain-native compression engine with negotiated models for each serialization family.**

ZChain is Zetako's proprietary lossless compression technology for blockchain data paths. This public repository documents the **evolution of the product**, benchmark methodology, blockchain-specific use cases, reproducible results, and the boundary between measured evidence and future integration work.

ZChain is not being developed as a generic “compress everything” library. A blockchain runtime already knows whether it is handling a block, receipt, RLP payload, signature, account key or consensus result. The product direction is to use that knowledge instead of spending CPU rediscovering it.

> **Goal:** reduce bytes moved or stored by blockchain infrastructure while keeping reconstruction exact and processing cost low enough for the path being optimized.

---

## Product evolution — from v3 to the Blockchain Engine

The public history is intentionally visible. Each generation exists because the previous one exposed a concrete limitation.

![ZChain product evolution](assets/zchain-v3-v4-speed-first.svg)

| Public generation | Engineering name | What changed | Evidence / delta |
|---|---|---|---|
| **ZChain 3 — Foundation** | v3 | Original context-adaptive lossless codec | Public compatibility baseline |
| **ZChain 4 — Fast Compatible** | v4 | C hot-path optimization without changing tested v3 output | **74.27 → 113.36 MiB/s encode** on the 64-file public corpus; same compressed byte count |
| **ZChain 5 — Speed** | Speed_First | New speed-oriented stream, lighter model, RAW bypass | **126.53 MiB/s encode / 74.67 MiB/s decode** on the same corpus |
| **ZChain 6 — Blockchain** | ZCB2 / blockchain profiles | First blockchain-specific routing and Ethereum hex specialization | Established that crypto-heavy fields should not always be treated like ordinary structured bytes |
| **ZChain 7 — Schema** | ZCB3 | Runtime-assisted structure / opaque / reference model | Architecture validated in research microbenchmarks; synthetic figures are not presented as production Reth/Agave throughput |
| **ZChain 8 — Blockchain Engine** | Blockchain C 1.1.x | Negotiated models, stable public C ABI, ZCB1 frames, installable SDK, multi-chain profile registry | Current product base |

### The change in architecture

```text
v3 / v4
structured bytes
      ↓
context model + arithmetic coding

Speed
      ↓
same concept, optimized for throughput

Blockchain
      ↓
payload-aware profiles

Schema
      ↓
structure | opaque crypto | known references

Blockchain Engine
      ↓
negotiated model selected by the blockchain runtime
```

The product therefore evolved from a standalone codec into a **blockchain compression engine with explicit integration contracts**.

---

## Current Ethereum milestone

The strongest current real-corpus specialization is the negotiated `ETHEREUM_HEX` profile on the Reth Ethereum JSON corpus.

| Path | Raw bytes | Final bytes | Savings | Encode | Decode |
|---|---:|---:|---:|---:|---:|
| Previous `ethereum-block` path | 2,908,507 | 559,234 | 80.77% | 99.32 MiB/s | 63.72 MiB/s |
| **Negotiated `ethereum-hex`** | **2,908,507** | **512,808** | **82.37%** | **146.12 MiB/s** | **129.41 MiB/s** |

On this corpus, the negotiated specialization is **8.3% smaller**, **1.47× faster to encode**, and **2.03× faster to decode** than the previous Ethereum path.

[Read the Ethereum page →](docs/ETHEREUM.md)

---

## Blockchain pages

Every page answers the same questions: **what do we compress, why does it matter, how is it measured, what is proven today, and what comes next?**

| Ecosystem | Current public status | Page |
|---|---|---|
| **Ethereum / Reth** | `ETHEREUM_HEX` supported; RLP/schema work next | [Ethereum](docs/ETHEREUM.md) |
| **EVM L2 / EVM-compatible** | Ethereum JSON-hex model reusable; chain-specific benches pending | [EVM L2](docs/EVM_L2.md) |
| **Solana** | Real mainnet RPC corpus measured; RPC profile supported | [Solana](docs/SOLANA.md) |
| **Agave** | Validator integration and shadow-path engineering | [Agave](docs/AGAVE.md) |
| **Cosmos / CometBFT** | Real RPC corpus measured; CometBFT RPC profile supported | [Cosmos / CometBFT](docs/COSMOS_COMETBFT.md) |
| **Bitcoin** | Native block / transaction adapter ready; specialized benchmark pending | [Bitcoin](docs/BITCOIN.md) |
| **Substrate / Cardano / Sui / Aptos / TRON / others** | Adapter-ready or research models | [Model matrix](docs/MODEL_MATRIX.md) |

---

## What ZChain can optimize

Depending on the chain and integration boundary, the recurring targets are:

- **RPC responses** — blocks, receipts, transactions, traces, account data;
- **storage and archive paths** — blocks, receipts, snapshots, exports, indexer datasets;
- **state sync / snapshot movement** — large structured batches where byte reduction can offset codec CPU;
- **service-to-service transfer** — when an explicit negotiated format is appropriate;
- **validator or execution-client sidecars** — shadow measurement before changing a critical path.

Compression savings on a benchmark payload are **not automatically equivalent to mainnet bandwidth or database savings**. Production claims require measurement in the actual path.

---

## Model maturity

ZChain uses explicit maturity labels:

- **SUPPORTED** — specialized model with real-corpus evidence and an integration contract;
- **ADAPTER_READY** — serialization/integration boundary is defined, but specialized real-corpus validation is still required;
- **RESEARCH** — active model work or architecture experiment.

[Open the model matrix →](docs/MODEL_MATRIX.md)

---

## Benchmark methodology

Public benchmark pages follow the same rules:

1. lossless byte-for-byte round trip;
2. exact workload and serialization named;
3. native codec timing separated from I/O, hashing and process startup;
4. compression and decompression throughput reported separately;
5. build profile and hardware stated;
6. p50/p95/p99 used for integration latency when available;
7. synthetic architecture tests labeled as synthetic;
8. no chain-specific production claim without a real chain-specific corpus.

[Benchmark methodology →](docs/METHODOLOGY.md)

---

## ZChain vs established codecs

ZChain is compared against **gzip, Zstd, Brotli and LZMA2** on the same documented payloads and presets.

The comparison is deliberately scoped. Zstd is the throughput leader on several tested workloads; ZChain's interest is where blockchain-specific structure can provide a different density / CPU tradeoff.

[Open the codec comparison →](docs/COMPRESSION_COMPARISON.md)

---

## Repository structure

```text
/
├── README.md                       product evolution and headline results
├── docs/
│   ├── ETHEREUM.md                 Ethereum / Reth
│   ├── EVM_L2.md                   Base / Arbitrum / Optimism / Polygon / BNB / Avalanche
│   ├── SOLANA.md                   Solana mainnet data
│   ├── AGAVE.md                    Agave validator integration
│   ├── COSMOS_COMETBFT.md          Cosmos / CometBFT
│   ├── BITCOIN.md                  Bitcoin model
│   ├── MODEL_MATRIX.md             remaining serialization families
│   ├── METHODOLOGY.md              benchmark rules
│   ├── COMPRESSION_COMPARISON.md   ZChain vs other codecs
│   └── evidence / legacy reports   detailed engineering evidence
└── assets/                         readable public charts
```

Detailed historical reports remain in the repository as evidence even when a shorter product-facing page links to them.

---

## Public vs private

**Published here:** methodology, benchmark reports, payload descriptions, product evolution, compatibility behavior, integration architecture and claim boundaries.

**Kept private:** proprietary codec core source, protected production releases, internal test infrastructure, unpublished datasets and customer-specific integrations.

For technical evaluation, partnership or licensing discussions: **contact@zetako.ai**

© Zetako. Proprietary technology. Public documentation in this repository does not grant rights to reproduce, reverse engineer or redistribute the protected ZChain implementation.