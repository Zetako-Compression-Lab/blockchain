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

## Current real-corpus result

Apple M4, in-memory C benchmark, same 2,908,507-byte Reth Ethereum JSON corpus:

| Path | Final bytes | Savings | Encode | Decode |
|---|---:|---:|---:|---:|
| Previous `ethereum-block` | 559,234 | 80.77% | 99.32 MiB/s | 63.72 MiB/s |
| **Negotiated `ethereum-hex`** | **512,808** | **82.37%** | **146.12 MiB/s** | **129.41 MiB/s** |

Relative to the previous Ethereum path:

- **8.3% smaller compressed output**;
- **1.47× faster encode**;
- **2.03× faster decode**.

These figures are specific to this corpus, hardware and build.

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

A native RLP profile will remain `RESEARCH` until spans come from actual client serialization and the same RLP bytes are compared against RAW, v4, Speed and the schema-assisted profile.

## Claim boundary

The current Ethereum JSON result proves codec behavior on the documented Reth corpus. It does **not** by itself prove P2P bandwidth reduction, database savings or production node latency. Those require path-specific integration tests.
