# Public Benchmark Methodology

[← Documentation index](README.md) · [Product evolution](../README.md)

ZChain benchmark pages are intended to be useful engineering evidence, not isolated marketing numbers. Every public result should make the workload, build and measurement boundary clear enough to reproduce or challenge the conclusion.

## Core rules

### 1. Exact round trip

Every measured compressed payload must reconstruct the original bytes exactly. Where the harness includes hashing, source and restored hashes must match.

### 2. Name the serialization

“Ethereum”, “Solana” or “Bitcoin” is not a workload. The report must identify the actual data type, for example:

- Ethereum JSON-RPC block;
- Ethereum receipt batch;
- RLP transaction batch;
- Solana `getBlock` full JSON;
- Agave shred payload;
- CometBFT `block_results` JSON;
- Bitcoin raw block batch.

### 3. Separate codec time from harness time

Native codec throughput excludes, unless explicitly stated otherwise:

- file loading;
- process startup;
- CSV output;
- SHA hashing;
- network fetch time.

Integration benchmarks may include adapter or copy cost, but that boundary must be stated.

### 4. Report encode and decode separately

Compression and decompression are independent costs. A codec that is attractive for archive creation may not be attractive for a read-heavy hot path.

### 5. State hardware and build

Public performance tables name the hardware and build profile. The current principal native benchmark platform is Apple M4 with optimized C release builds. Results from development builds are not mixed with release-codec claims.

### 6. Compare identical input bytes

Codec comparisons use the same input for every codec. Presets and quality levels are named explicitly.

### 7. Synthetic means synthetic

Architecture microbenchmarks are useful for proving a design direction. They are not relabeled as production Reth, Agave or validator throughput.

### 8. No automatic extrapolation to production savings

A 50% smaller RPC file does not automatically mean 50% less validator traffic or 50% less database storage. Every enabled production path needs its own measurement.

---

## Required metrics

A mature benchmark should report as many of the following as the harness supports:

| Metric | Purpose |
|---|---|
| raw bytes | input scale |
| compressed bytes | absolute output size |
| savings / % original | density |
| encode MiB/s | write / send-side cost |
| decode MiB/s | read / receive-side cost |
| ns/byte | CPU-normalized view on the measured host |
| p50 / p95 / p99 | latency distribution |
| workspace bytes | memory cost per worker |
| round-trip status | correctness |

## Profile comparison order

For a new blockchain-native model, the preferred comparison is:

```text
RAW
  vs
v4 compatibility
  vs
Speed
  vs
new negotiated blockchain model
```

For established-codec comparison, use the same input with named presets for the codecs included in the public comparison set.

## Schema-assisted benchmark rule

When the blockchain runtime supplies field boundaries, the timed integration benchmark must state whether span construction / serialization is included.

The strongest evidence is an end-to-end serializer path where the runtime emits structural, opaque and reference segments directly rather than constructing a large buffer and scanning it afterward.

## Promotion to SUPPORTED

A model should not be labeled `SUPPORTED` merely because its registry entry exists. A strong promotion gate includes:

1. real corpus from the named chain / serialization family;
2. exact round trip;
3. native optimized build;
4. sanitizer validation of the model implementation;
5. portable build validation;
6. repeatable benchmark command;
7. public result table;
8. explicit claim boundary.

## Reproducibility and evidence

The shorter ecosystem pages are product-facing summaries. Detailed legacy reports, CSVs and engineering notes remain in this repository where available so that the evolution can be inspected rather than rewritten away.
