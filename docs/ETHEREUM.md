# Ethereum / Reth — ZChain

[← Product evolution](../README.md) · [Methodology](METHODOLOGY.md) · [Codec comparison](COMPRESSION_COMPARISON.md)

Ethereum is currently ZChain's strongest public specialization because execution-layer data contains a useful mix of **highly repetitive structure** and **cryptographic material that should not always be processed like normal text**.

## What we compress

Current public evidence covers two distinct Ethereum surfaces:

- **JSON-RPC**: blocks, receipts, logs, topics, bloom filters and `0x`-encoded payloads through the negotiated `ETHEREUM_HEX` model;
- **native RLP**: Alloy/Reth-compatible transaction serialization through the schema-assisted `ETHEREUM_SCHEMA` model.

## Why it matters

Ethereum infrastructure repeatedly moves large structured payloads between nodes, RPC services, indexers, archives and analytics systems. A useful codec can target:

- RPC response bytes;
- receipt/log export size;
- native transaction and block-body serialization;
- archive and snapshot movement;
- internal service-to-service transfer;
- future storage or network paths when the CPU/latency economics are proven.

---

## Ethereum model evolution

### 1. Generic / compatibility phase

The first public Reth corpus established that ZChain compresses Ethereum blocks and receipts losslessly, but the codec treated the entire payload as one generic structured stream.

### 2. Negotiated `ETHEREUM_HEX`

The blockchain-specialized path removed the need for a global profile scan. The caller explicitly selects the Ethereum hex model through the frame API.

This profile remains the supported path for Ethereum JSON-RPC workloads.

### 3. Native `ETHEREUM_SCHEMA`

The next architectural step moves below JSON. Reth/Alloy already knows field identity while serializing native transactions, so ZChain uses that knowledge directly.

```text
Alloy / Reth objects
       │
       ▼
RLP serialization
       │
       ├── structural bytes ───────────────> ZChain model
       ├── hashes / signatures / crypto ──> opaque path
       └── known repeated values ─────────> references
                          │
                          ▼
                 ETHEREUM_SCHEMA
                          │
                          ▼
                         ZCB1
```

There is **no RLP parser inside ZChain's timed codec path** and no `.ops` replay file in the supported direct adapter path.

---

## Supported native RLP benchmark

Reference corpus:

- **24 real Ethereum blocks** fetched through JSON-RPC;
- **5,246 native Alloy transactions**;
- transaction mix including legacy, EIP-1559, EIP-4844 blob and EIP-7702;
- **2,959,206 native RLP bytes**;
- Apple M4, release/O3 native benchmark.

### Same-RLP codec comparison

| Codec | Output bytes | Savings | Encode | Decode |
|---|---:|---:|---:|---:|
| ZChain 4 — Fast Compatible | 1,102,352 | 62.75% | 79.50 MiB/s | 46.37 MiB/s |
| ZChain 5 — Speed | 1,102,219 | 62.75% | 91.24 MiB/s | 55.14 MiB/s |
| ZChain 8 — `ethereum-rlp` compatible | 1,102,352 | 62.75% | 74.23 MiB/s | 46.12 MiB/s |
| **ZChain 8 — `ETHEREUM_SCHEMA`** | **1,070,680** | **63.82%** | **108.24 MiB/s** | **75.51 MiB/s** |

Against ZChain 5 Speed on the exact same RLP bytes, `ETHEREUM_SCHEMA` is:

- **2.86% smaller**;
- **18.6% faster to encode** in codec-only mode;
- **36.9% faster to decode**.

![Ethereum native RLP benchmark](../assets/zchain-ethereum-schema-native.svg)

### Serializer-to-frame measurement

The direct Alloy adapter also reports a separate end-to-end serialization measurement:

| Measurement | Input | ZCB1 bytes | Savings | Encode | Decode |
|---|---:|---:|---:|---:|---:|
| **Alloy serializer → ZChain frame** | 2,959,206 | **1,070,680** | **63.82%** | **58.28 MiB/s** | **74.02 MiB/s** |

This timing includes Alloy transaction serialization, object-derived span marking and ZChain frame generation. It is deliberately not presented as codec-only throughput.

[Open the full direct Alloy/Reth supported report →](ZCHAIN_8_ETHEREUM_SCHEMA_DIRECT_ALLOY_SUPPORTED_REPORT.md)

---

## Supported Ethereum JSON benchmark

The negotiated `ETHEREUM_HEX` model remains the supported JSON-RPC specialization.

Apple M4, native C release, same 2,908,507-byte Reth Ethereum JSON corpus:

| Path | Final bytes | Savings | Encode | Decode | Encode ns/B | Decode ns/B |
|---|---:|---:|---:|---:|---:|---:|
| Previous `ethereum-block` | 559,234 | 80.77% | 91.83 MiB/s | 57.70 MiB/s | 10.386 | 16.528 |
| **Negotiated `ethereum-hex`** | **512,808** | **82.37%** | **131.11 MiB/s** | **114.69 MiB/s** | **7.274** | **8.315** |

Relative to the previous Ethereum path:

- **8.30% smaller compressed output**;
- **1.43× faster encode**;
- **1.99× faster decode**.

[Open the multi-chain M4 benchmark report →](ZCHAIN_BLOCKCHAIN_C_1_2_0_M4_BENCH_REPORT.md)

---

## Why schema assistance changes the result

The schema model separates data according to information already available in the blockchain runtime:

| Span type | Typical Ethereum content | Strategy |
|---|---|---|
| `STRUCTURAL` | RLP prefixes, lengths, tx type markers, structured numeric fields | ZChain structural model |
| `OPAQUE` | signatures, hashes, roots, entropy-heavy crypto fields | bypass probabilistic modeling |
| `REFERENCEABLE` | values that may be reused later | stored once, assigned reference id |
| `KNOWN_REF` | runtime-known repeated values | direct reference, no search required |
| `REF_RUN` | adjacent known references | compact reference run |

The benefit is not that ZChain parses Ethereum better after the fact. The benefit is that **the runtime tells ZChain what it already knows**.

---

## Validation and support status

`ETHEREUM_SCHEMA` is now **SUPPORTED** because the reference path satisfies the intended support gate:

- real Ethereum blocks;
- native Alloy objects;
- exact RLP reconstruction;
- direct `zchain_bc_stream_*` integration;
- no `.ops` bridge in the supported final path;
- `make test` passing;
- adapter `cargo check` passing;
- distribution build passing;
- measured gain against ZChain 5 Speed on the same native RLP bytes.

## Claim boundary

The current public evidence proves codec behavior on the documented Ethereum JSON and native RLP corpora. It does **not** automatically prove equivalent P2P bandwidth reduction, database savings or execution-client latency improvements. Those require measurements in the exact production path.

Historical evidence remains in [the Reth benchmark report](zchain-reth-benchmarks.md).
