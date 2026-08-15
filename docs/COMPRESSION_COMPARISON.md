# ZChain vs Established Codecs

[← Product evolution](../README.md) · [Methodology](METHODOLOGY.md)

This page compares ZChain with selected established codecs on the **same documented blockchain-oriented payloads and named presets**.

The comparison is deliberately scoped. ZChain does not claim to beat every general-purpose codec on every dataset. In particular, **Zstd is the throughput benchmark to beat** on several tested workloads.

> The table below uses the same native single-thread comparison harness. The newer negotiated `ETHEREUM_HEX` product profile has not yet been rerun against every competitor in this exact harness, so it is not mixed into this table.

---

## Ethereum receipts — 1 MiB

| Codec | Final size | % original | Encode | Decode |
|---|---:|---:|---:|---:|
| **ZChain Speed** | **43,318 B** | **4.13%** | **170 MiB/s** | **113 MiB/s** |
| gzip-1 | 74,316 B | 7.09% | 446 MiB/s | 1,292 MiB/s |
| gzip-6 | 61,489 B | 5.86% | 185 MiB/s | 1,495 MiB/s |
| **Zstd-1** | 43,799 B | 4.18% | **1,875 MiB/s** | **4,124 MiB/s** |
| Zstd-3 | 58,832 B | 5.61% | 1,292 MiB/s | 3,849 MiB/s |
| Brotli q1 | 46,463 B | 4.43% | 1,279 MiB/s | 1,845 MiB/s |
| **Brotli q5** | **42,279 B** | **4.03%** | 159 MiB/s | 2,127 MiB/s |
| **LZMA2 p1** | **29,842 B** | **2.85%** | 101 MiB/s | 659 MiB/s |
| LZMA2 p5 | 43,876 B | 4.18% | 12.6 MiB/s | 562 MiB/s |

![Ethereum receipts codec comparison](../assets/zchain-ethereum-receipts-comparison.svg)

### What this says

**Against gzip-6:** ZChain produces substantially fewer bytes while encode throughput is in the same order of magnitude.

**Against Brotli q5:** compressed size is very close; ZChain is slightly faster to encode on this workload, while Brotli decodes much faster.

**Against LZMA2:** LZMA2 p1 wins density, but at lower encode throughput. LZMA2 p5 lands near ZChain's size while encoding far more slowly.

**Against Zstd-1:** this is the most important competitive result. Zstd-1 produces almost the same size — **43,799 B vs 43,318 B** — while encoding about **11× faster** and decoding about **36× faster** in this harness.

That is why current ZChain R&D focuses on **blockchain-specific information routing and schema assistance**, not on pretending a generic arithmetic hot loop can simply be micro-optimized into Zstd-like throughput.

---

## Large structured JSON — `blocks-4m`

| Codec | Final size | Encode | Decode |
|---|---:|---:|---:|
| **ZChain Speed** | **165,517 B** | **169.9 MiB/s** | 106.3 MiB/s |
| gzip-6 | 333,937 B | 104.9 MiB/s | 1,382 MiB/s |
| Zstd-1 | 255,820 B | **1,379 MiB/s** | **2,958 MiB/s** |
| Brotli q5 | 253,272 B | 128.4 MiB/s | 1,695 MiB/s |
| LZMA2 p1 | 226,982 B | 66.9 MiB/s | 452 MiB/s |

On this particular structured JSON workload, ZChain produces the smallest output in the table while encoding faster than gzip-6, Brotli q5 and LZMA2 p1. Zstd remains dramatically faster, but produces a larger output here.

---

## Where ZChain is strong today

The evidence supports a narrower, more useful statement than “best compressor”:

> **ZChain can achieve unusually strong density on some structured blockchain payloads, with encode throughput that is competitive against heavier-density presets such as Brotli q5 or LZMA2 — while Zstd remains the dominant general-purpose throughput reference.**

The product strategy is therefore to exploit knowledge that generic codecs do not receive: serialization family, cryptographic fields, repeated runtime-known values and blockchain-specific structure.

---

## Important caveats

- All results are workload- and preset-specific.
- LZMA2 is the compression engine commonly associated with 7-Zip; these measurements are not a complete `.7z` container benchmark.
- Random/RAW multi-GiB/s figures from earlier experiments represent in-cache memory behavior, not disk or network throughput.
- Competitor implementations can often be tuned further; these tables describe the documented harness, not theoretical maxima.
- Decode remains a major area where established codecs, especially Zstd, are substantially faster.

## Next comparison

The next valuable competitive run is the **industrialized negotiated blockchain profiles** — especially `ETHEREUM_HEX` and future native RLP/schema models — against Zstd, gzip and Brotli on the exact same real chain corpus.
