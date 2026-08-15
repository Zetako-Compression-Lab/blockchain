# ZChain Compression Comparison

This document presents selected public benchmark comparisons between **ZChain Speed_First** and established general-purpose codecs on structured blockchain-oriented payloads.

The purpose is not to claim universal superiority. Results are **workload-specific** and should be interpreted together with the exact payload type, codec preset, build, and hardware used for the test.

> **Note:** this public comparison intentionally focuses on gzip, Brotli, and LZMA2/7zip.

---

## Ethereum receipts — 1 MiB

Ethereum receipt payloads are highly structured and repetitive: logs, addresses, topics, bloom filters, and hexadecimal fields create a strong compression opportunity.

| Codec | Final size | % of original | Compression | Decompression |
|---|---:|---:|---:|---:|
| **ZChain Speed_First** | **43,318 B** | **4.13%** | **170 MiB/s** | **113 MiB/s** |
| gzip-1 | 74,316 B | 7.09% | 446 MiB/s | 1,292 MiB/s |
| gzip-6 | 61,489 B | 5.86% | 185 MiB/s | 1,495 MiB/s |
| Brotli q1 | 46,463 B | 4.43% | 1,279 MiB/s | 1,845 MiB/s |
| **Brotli q5** | **42,279 B** | **4.03%** | 159 MiB/s | 2,127 MiB/s |
| LZMA2 / 7zip p1 | **29,842 B** | **2.85%** | 101 MiB/s | 659 MiB/s |
| LZMA2 / 7zip p5 | 43,876 B | 4.18% | 12.6 MiB/s | 562 MiB/s |

### ZChain vs gzip-6

On this workload, ZChain Speed_First produces a substantially smaller output while operating at roughly the same compression speed:

- ZChain: **43,318 B**, **170 MiB/s**
- gzip-6: **61,489 B**, **185 MiB/s**

The ZChain output is about **30% smaller** than gzip-6 while encode throughput remains in the same order of magnitude.

### ZChain vs Brotli q5

Brotli q5 and ZChain land very close in compressed size:

- ZChain: **43,318 B**
- Brotli q5: **42,279 B**

The difference is about **2.4%**, while ZChain is slightly faster in compression on this test:

- ZChain: **170 MiB/s**
- Brotli q5: **159 MiB/s**

### ZChain vs LZMA2 / 7zip

LZMA2 can reach a smaller output on this Ethereum receipt workload, especially at preset 1, but it pays a larger compression-speed cost.

Compared with LZMA2 preset 5:

- ZChain encode: **170 MiB/s**
- LZMA2 p5 encode: **12.6 MiB/s**

ZChain is therefore about **13.5× faster** in compression on this benchmark while producing a very similar final size.

Against LZMA2 preset 1, LZMA2 compresses more aggressively, but ZChain remains about **1.7× faster** in compression.

---

## Large structured JSON — `blocks-4m`

This workload highlights the opposite side of the tradeoff: ZChain achieves both strong compression density and competitive encode throughput on a large structured blockchain-style JSON payload.

| Codec | Final size | Encode |
|---|---:|---:|
| **ZChain Speed_First** | **165 KB** | **170 MiB/s** |
| gzip-6 | 334 KB | 105 MiB/s |
| Brotli q5 | 253 KB | 128 MiB/s |
| LZMA2 p1 | 227 KB | 67 MiB/s |

### Interpretation

On this workload, ZChain produces:

- about **50% less data than gzip-6**;
- about **35% less data than Brotli q5**;
- about **27% less data than LZMA2 p1**.

At the same time, ZChain encode throughput is:

- about **1.62× faster than gzip-6**;
- about **1.33× faster than Brotli q5**;
- about **2.54× faster than LZMA2 p1**.

This is a particularly relevant result for blockchain-oriented structured JSON, where reducing network/storage volume without sacrificing encode latency is often more important than optimizing for archival compression alone.

---

## Public positioning

These results support a narrow and useful claim:

> **On selected structured blockchain payloads, ZChain Speed_First can match or exceed the compression density of gzip and Brotli while maintaining competitive or higher encode throughput; LZMA2 can compress more aggressively on some payloads, but at a significant encode-speed cost.**

The benchmark should not be generalized to unrelated datasets without additional measurements.

## Reporting rules

When quoting these comparisons publicly:

- always name the workload;
- always include codec preset/quality level;
- keep compression and decompression throughput separate;
- do not convert one workload result into a universal codec claim;
- preserve the exact measured values rather than extrapolating them.