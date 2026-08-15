# ZChain 8 Ethereum Schema — Direct Alloy/Reth Supported Benchmark

[← Ethereum page](ETHEREUM.md) · [Product evolution](../README.md) · [Methodology](METHODOLOGY.md)

This report documents the first **SUPPORTED native Ethereum RLP model** in ZChain 8.

The important change is architectural: `ETHEREUM_SCHEMA` is driven directly from native Alloy transaction objects. The adapter calls the public `zchain_bc_stream_*` writer API while preserving the exact RLP byte stream. There is **no `.ops` replay file and no RLP parser inside ZChain's timed codec path**.

## Status

**Model:** `ETHEREUM_SCHEMA`  
**Maturity:** `SUPPORTED`  
**Integration:** direct Alloy/Reth-compatible object adapter  
**Hardware:** Apple M4  
**Build:** release / O3 native  
**Round-trip:** exact byte reconstruction checked by the benchmark harness

## Corpus

The supported reference corpus contains:

- **24 real Ethereum blocks** fetched through JSON-RPC;
- **5,246 native Alloy transactions**;
- transaction mix including **legacy, EIP-1559, EIP-4844 blob and EIP-7702** transactions;
- **2,959,206 native RLP input bytes**.

The benchmark compares all codecs on the **same exported RLP bytes**.

## Direct Alloy measurements

Two timings are intentionally reported separately.

| Measurement | Input bytes | ZCB1 bytes | Savings | Encode | Decode |
|---|---:|---:|---:|---:|---:|
| **Codec-only direct stream** | 2,959,206 | **1,070,680** | **63.82%** | **108.24 MiB/s** | **75.51 MiB/s** |
| **Alloy serializer → ZChain frame** | 2,959,206 | **1,070,680** | **63.82%** | **58.28 MiB/s** | **74.02 MiB/s** |

`codec-only` measures prebuilt in-memory Alloy bytes plus object-derived span knowledge through the ZChain stream writer.

`serializer_to_frame` includes Alloy transaction serialization, object-derived span marking and ZChain frame generation. RPC fetch is outside the timed section.

## Same-RLP baseline comparison

| Codec | Output bytes | Savings | Encode | Decode |
|---|---:|---:|---:|---:|
| ZChain 4 — Fast Compatible | 1,102,352 | 62.75% | 79.50 MiB/s | 46.37 MiB/s |
| ZChain 5 — Speed | 1,102,219 | 62.75% | 91.24 MiB/s | 55.14 MiB/s |
| ZChain 8 — `ethereum-rlp` compatible | 1,102,352 | 62.75% | 74.23 MiB/s | 46.12 MiB/s |
| **ZChain 8 — `ETHEREUM_SCHEMA`** | **1,070,680** | **63.82%** | **108.24 MiB/s** | **75.51 MiB/s** |

Against ZChain 5 Speed on the same RLP bytes, the supported ZChain 8 schema model is:

- **2.86% smaller**;
- **18.6% faster to encode** in codec-only mode;
- **36.9% faster to decode**.

![Ethereum native RLP benchmark](../assets/zchain-ethereum-schema-native.svg)

## Why the schema model wins

The runtime already knows which bytes it is serializing. ZChain uses that knowledge instead of rediscovering it by scanning the completed payload.

```text
Alloy / Reth transaction objects
            │
            ▼
       RLP serialization
            │
     ┌──────┴───────────┐
     │                  │
 structural bytes   crypto / opaque bytes
     │                  │
 ZChain model       sidecar / references
     │                  │
     └────────┬─────────┘
              ▼
      ETHEREUM_SCHEMA
              │
              ▼
             ZCB1
```

The stream contract supports:

- `STRUCTURAL` — RLP prefixes, lengths, transaction type markers and structured values;
- `OPAQUE` — hashes, signatures and other entropy-heavy fields;
- `REFERENCEABLE` — values that may be reused later;
- `KNOWN_REF` — runtime-known repeated values without a hash/search step;
- `REF_RUN` — compact groups of adjacent known references.

## Support boundary

This benchmark supports the following scoped statement:

> **On the documented 24-block, 5,246-transaction native Ethereum RLP corpus, ZChain 8 `ETHEREUM_SCHEMA` produces smaller output and higher codec-only encode/decode throughput than ZChain 5 Speed on the same RLP bytes.**

It does not by itself claim equivalent gains for Ethereum P2P traffic, database storage or every Reth execution path. Those require path-specific integration measurements.

## Validation used for promotion

The implementation was promoted to `SUPPORTED` after:

- direct Alloy object integration through `zchain_bc_stream_*`;
- exact round-trip validation;
- `make test` passing;
- Alloy/Reth adapter `cargo check` passing;
- distribution build (`make dist`) passing;
- same-RLP comparison against ZChain 4, ZChain 5 and the ZChain 8 compatible RLP path.

## Related evidence

- [Ethereum / Reth product page](ETHEREUM.md)
- [Public benchmark methodology](METHODOLOGY.md)
- [Earlier ZChain 1.2.0 M4 reference report](ZCHAIN_BLOCKCHAIN_C_1_2_0_M4_BENCH_REPORT.md)
