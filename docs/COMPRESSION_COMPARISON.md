# ZChain vs Established Codecs

[← Product evolution](../README.md) · [Methodology](METHODOLOGY.md)

This page compares ZChain with selected established codecs on the **same documented blockchain-oriented payloads and named presets**.

The comparison is deliberately scoped. ZChain does not claim to be the best compressor on every dataset; the goal is to show where its blockchain-focused density / throughput tradeoff is useful.

> The table below uses the same native single-thread comparison harness. The newer negotiated `ETHEREUM_HEX` product profile has not yet been rerun against every competitor in this exact harness, so it is not mixed into this table.

---

## Ethereum receipts — 1 MiB

| Codec | Final size | % original | Encode | Decode |
|---|---:|---:|---:|---:|
| **ZChain Speed** | **43,318 B** | **4.13%** | **170 MiB/s** | **113 MiB/s** |
| gzip-1 | 74,316 B | 7.09% | 446 MiB/s | 1,292 MiB/s |
| gzip-6 | 61,489 B | 5.86% | 185 MiB/s | 1,495 MiB/s |
| Brotli q1 | 46,463 B | 4.43% | 1,279 MiB/s | 1,845 MiB/s |
| **Brotli q5** | **42,279 B** | **4.03%** | 159 MiB/s | 2,127 MiB/s |
| **LZMA2 p1** | **29,842 B** | **2.85%** | 101 MiB/s | 659 MiB/s |
| LZMA2 p5 | 43,876 B | 4.18% | 12.6 MiB/s | 562 MiB/s |

![Ethereum receipts codec comparison](../assets/zchain-ethereum-receipts-comparison.svg)

### Against gzip-6

ZChain produces **43,318 B** versus **61,489 B** for gzip-6 — about **30% less compressed data** — while encode throughput remains close: **170 MiB/s vs 185 MiB/s**.

### Against Brotli q5

Brotli q5 produces **42,279 B**, only about **2.4% smaller** than ZChain's 43,318 B. In this test ZChain encodes slightly faster: **170 MiB/s vs 159 MiB/s**.

### Against LZMA2

LZMA2 preset 1 reaches the smallest output in this table at **29,842 B**, but ZChain encodes about **1.7× faster**.

At preset 5, LZMA2 produces a very similar final size to ZChain — **43,876 B vs 43,318 B** — while ZChain encodes about **13.5× faster**.

### What this workload says

On this receipts workload, ZChain sits in a useful middle ground: materially denser than gzip-6, nearly the same size as Brotli q5 at slightly higher encode throughput, and much faster than LZMA2 p5 while landing at a similar size.

Its decompression throughput is lower than the listed general-purpose codecs, which remains an important optimization area for read-heavy paths.

---

## Large structured JSON — `blocks-4m`

| Codec | Final size | Encode | Decode |
|---|---:|---:|---:|
| **ZChain Speed** | **165,517 B** | **169.9 MiB/s** | 106.3 MiB/s |
| gzip-6 | 333,937 B | 104.9 MiB/s | 1,382 MiB/s |
| Brotli q5 | 253,272 B | 128.4 MiB/s | 1,695 MiB/s |
| LZMA2 p1 | 226,982 B | 66.9 MiB/s | 452 MiB/s |

On this structured JSON workload, ZChain produces the smallest output in the table while encoding faster than gzip-6, Brotli q5 and LZMA2 p1.

---

## Why these comparisons matter for blockchain systems

Different paths value different tradeoffs:

| Use case | What usually matters most |
|---|---|
| RPC response generation | encode latency + bytes transferred |
| Receipt/log export | density + sustained throughput |
| Snapshot/archive creation | density may matter more than latency |
| Validator hot path | latency and CPU dominate; integration must be proven separately |
| Analytics/data movement | balance between bytes, encode throughput and decode throughput |

The benchmark therefore reports both **size** and **speed** instead of declaring one codec globally best.

---

## Important caveats

- All results are workload- and preset-specific.
- LZMA2 is the compression engine commonly associated with 7-Zip; these measurements are not a complete `.7z` container benchmark.
- Random/RAW multi-GiB/s figures from earlier experiments represent in-cache memory behavior, not disk or network throughput.
- Competitor implementations can often be tuned further; these tables describe the documented harness, not theoretical maxima.
- Decode performance remains an active optimization area for ZChain.

## Next comparison

The next valuable competitive run is the **industrialized negotiated blockchain profiles** — especially `ETHEREUM_HEX` and future native RLP/schema models — on the exact same real chain corpus.
