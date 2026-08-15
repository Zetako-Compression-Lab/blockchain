# Solana — ZChain

[← Product evolution](../README.md) · [Agave integration](AGAVE.md) · [Methodology](METHODOLOGY.md)

This page covers **Solana data as a compression workload**. It is intentionally separate from the Agave page, which focuses on validator-side integration architecture.

## What we compress

The public Solana mainnet corpus contains:

- `getBlock` with full transaction details;
- `getBlock` with signatures only;
- `getBlockCommitment`;
- `getBlockProduction`;
- sampled `getTransaction` responses.

The current public corpus contains **21 files / 26,106,696 raw bytes**.

## Why it matters

Full Solana block responses combine account keys, signatures, instructions, balances, metadata, logs and repeated JSON structure. Potential benefits include:

- smaller RPC responses;
- lower bandwidth for block-data services and indexers;
- smaller exports and archive transfers;
- evidence for deciding whether deeper validator/storage integration is worth testing.

## Latest supported profile result

Latest reference run: Apple M4, native C release, `-O3 -DNDEBUG -mcpu=native`, `solana-rpc`, 30 iterations.

| Files | Raw bytes | ZChain bytes | Savings | Encode | Decode | Encode ns/B | Decode ns/B |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 21 | 26,106,696 | **4,493,986** | **82.79%** | **110.40 MiB/s** | **67.93 MiB/s** | 8.638 | 14.039 |

All benchmarked files round-trip exactly, and the release passed the package test and sanitizer gates.

[Open the full 1.2.0 M4 benchmark report →](ZCHAIN_BLOCKCHAIN_C_1_2_0_M4_BENCH_REPORT.md)

## Historical profile evolution

Earlier public release measurements remain useful for showing how the generic codec evolved before the current blockchain engine.

| Workload | v4 encode | v4 decode | v4 behavior | Speed encode | Speed decode | Size delta vs v4 |
|---|---:|---:|---|---:|---:|---:|
| `getBlock` full JSON | **124.35** | **74.99** | **84.18% savings** | **138.55** | **81.98** | +1.78% |
| signatures-only JSON | 36.53 | 21.30 | v3-compatible output | 40.66 | 23.01 | +0.97% |
| `getBlockCommitment` | 48.91 | 42.10 | v3-compatible output | 56.15 | 37.69 | +0.00% |
| `getBlockProduction` | 38.20 | 22.55 | v3-compatible output | 42.37 | 24.34 | **-1.41%** |
| `getTransaction` | 46.46 | 35.77 | v3-compatible output | 53.65 | 31.59 | **-0.07%** |

Those figures are historical profile measurements, not replacements for the latest 1.2.0 reference run above.

## Current model status

`solana-rpc-json` is **SUPPORTED** in the public model registry because the serialization family and real mainnet RPC corpus are established.

The more ambitious native Solana model is still separate work: signatures, recent blockhashes and account keys are natural candidates for opaque/reference-aware handling rather than generic byte-by-byte modeling.

## What comes next

A native Solana model should be benchmarked on the exact serialized bytes produced by the integration path, with:

- signatures as opaque data;
- repeated recent blockhash values as references where the runtime knows identity;
- account keys and repeated account-key arrays as schema-provided segments;
- structural metadata and compact indexes kept in the ZChain model.

That work belongs with the Agave integration layer rather than with JSON auto-detection.

## Claim boundary

These are codec measurements on real Solana RPC payloads. They are not claims about TPU/TVU traffic, shred wire savings or blockstore storage until those exact paths are benchmarked independently.

Detailed historical evidence remains in [SOLANA_MAINNET_BENCHMARKS.md](SOLANA_MAINNET_BENCHMARKS.md).
