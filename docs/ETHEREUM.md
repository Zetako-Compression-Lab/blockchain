# Ethereum / Reth — ZChain

[← Product evolution](../README.md) · [Methodology](METHODOLOGY.md) · [Codec comparison](COMPRESSION_COMPARISON.md)

Ethereum is currently ZChain's strongest public specialization because execution-layer data contains a useful mix of **highly repetitive structure** and **cryptographic material that should not always be processed like normal text**.

## What we compress

Current public evidence covers:

- `eth_getBlockByNumber` responses with full transactions;
- `eth_getBlockReceipts` responses;
- block metadata, addresses, transaction fields, logs, topics, bloom filters and `0x`-encoded payloads;
- future integration work: native RLP transaction, receipt and block-body batches.

## Why it matters

Ethereum infrastructure repeatedly moves large structured payloads between nodes, RPC services, indexers, archives and analytics systems. A useful codec can target:

- RPC response bytes;
- receipt/log export size;
- archive and snapshot movement;
- internal service-to-service transfer;
- future storage or native serialization paths when the CPU/latency economics are proven.

## Evolution of the Ethereum model

### Generic / compatibility phase

The first public Reth corpus established that ZChain compresses Ethereum blocks and receipts losslessly, but the codec treated the entire payload as one generic structured stream.

### Blockchain specialization

The first blockchain prototype recognized that large hex-heavy regions behave differently from ordinary structured bytes. It improved Ethereum results, but automatic scanning added cost on other payloads.

### Negotiated `ETHEREUM_HEX`

The production direction removes that global scan. The caller explicitly selects the Ethereum hex profile through the frame API.

That means the blockchain integration says **what kind of payload it is**; ZChain does not search arbitrary input trying to infer it.

## Latest real-corpus result

Latest reference run: Apple M4, native C release, `-O3 -DNDEBUG -mcpu=native`, 50 iterations, same 2,908,507-byte Reth Ethereum JSON corpus.

| Path | Final bytes | Savings | Encode | Decode | Encode ns/B | Decode ns/B |
|---|---:|---:|---:|---:|---:|---:|
| Previous `ethereum-block` | 559,234 | 80.77% | 91.83 MiB/s | 57.70 MiB/s | 10.386 | 16.528 |
| **Negotiated `ethereum-hex`** | **512,808** | **82.37%** | **131.11 MiB/s** | **114.69 MiB/s** | **7.274** | **8.315** |

Relative to the previous Ethereum path:

- **8.30% smaller compressed output**;
- **1.43× faster encode**;
- **1.99× faster decode**.

All benchmarked files round-trip exactly, and the release passed `make test` plus ASan/UBSan validation.

[Open the full 1.2.0 M4 benchmark report →](ZCHAIN_BLOCKCHAIN_C_1_2_0_M4_BENCH_REPORT.md)

## Receipts as a compression workload

Receipts are particularly interesting because logs, addresses, topics, bloom filters and repeated JSON keys create strong structure while transaction-specific hashes and payloads introduce entropy.

Historical public measurements are retained in [the Reth benchmark evidence](zchain-reth-benchmarks.md).

## Next model: native RLP / schema-assisted Ethereum

The next serious Ethereum step is **not another JSON scanner**. The target is a native integration where Reth / Alloy already knows field boundaries.

Conceptually:

```text
Reth / Alloy objects
       │
       ├── RLP structure, lengths, small integers ──> ZChain structural model
       ├── hashes / roots / signatures ─────────────> opaque path
       └── repeated known values ───────────────────> references
                          │
                          ▼
                 negotiated ZCB1 frame
```

### Current `ETHEREUM_SCHEMA` status

`ETHEREUM_SCHEMA` now has an explicit stream-writer implementation path and the smoke example emits:

```text
encoding=3 frame=2430
```

The smoke path passes sanitizer-covered round-trip tests. **No supported-performance number is published yet.** The remaining requirement is real Reth / Alloy RLP bytes plus runtime-provided spans.

A native RLP profile will remain `RESEARCH` until the same real RLP bytes are compared against RAW, v4, Speed and the schema-assisted path.

## Claim boundary

The current Ethereum JSON result proves codec behavior on the documented Reth corpus. It does **not** by itself prove P2P bandwidth reduction, database savings or production node latency. Those require path-specific integration tests.
