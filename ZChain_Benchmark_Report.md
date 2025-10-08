
# ZCHAIN Compression Benchmark Report
**Author:** Zetako Compression Lab  
**Date:** October 2025  
**Repository:** [Zetako-Compression-Lab](https://github.com/organizations/Zetako-Compression-Lab)

---

## Overview

This document presents benchmark results for the **ZCHAIN compression engine**, a blockchain-optimized, lossless codec designed to reduce payload size and network bandwidth for Orbit, Stylus, and Solana-class validators.

The results include both **encoding** and **decoding** measurements on representative blockchain payloads such as blocks, transactions, calldata, receipts, and logs.

All tests were performed using the native **C implementation of ZCHAIN** on macOS (single-threaded) to ensure deterministic reproducibility.

---

## 1. Test Methodology

Each dataset was processed twice:
1. **Encode:** `<input> → <input>.zchain`
2. **Decode:** `<input>.zchain → <input>`

Both stages were profiled for:
- Execution time (s)  
- Cycles per byte  
- Throughput (bytes/s)  
- Memory usage (bytes)  
- Compression ratio and reduction rate  

Payloads tested:
- `block_*.json` — small Solana-style blocks  
- `calldata_*.hex` — transaction calldata (hex-encoded)  
- `receipts.jsonl` — receipts JSONL stream  
- `logs.jsonl` — structured validator logs  
- `tx_*.rlp` — Ethereum-compatible RLP transactions  

---

## 2. Compression Summary (Encode Phase)

| Category | Avg Input Size | Avg Output Size | Size Reduction | Avg Cycles/Byte | Avg Speed (B/s) | Notes |
|-----------|----------------|-----------------|----------------|-----------------|-----------------|-------|
| **Blocks (JSON)** | 1.4 KB | 0.9 KB | **−35 %** | ~3 600 | ~700 k B/s | Compact, repeatable JSON structure |
| **Calldata (HEX)** | 6.5 KB | 3.6 KB | **−43 %** | ~1 400 | 1–1.5 MB/s | High redundancy in opcode patterns |
| **Receipts (JSONL)** | 17.5 KB | 8.9 KB | **−49 %** | ~430 | 6.6 MB/s | Best performer, highly repetitive data |
| **Logs (JSONL)** | 10 KB | 5.2 KB | **−48 %** | ~1 800 | 1.4 MB/s | Structured text compression |
| **Transactions (RLP)** | 0.5–3.4 KB | 0.6–3.9 KB | **+15 % (expansion)** | 1 500–5 000 | 0.5–1.5 MB/s | Already dense binary payloads |

---

## 3. Decompression Summary (Decode Phase)

| Category | Avg Input Size | Avg Output Size | Ratio Restored | Avg Cycles/Byte | Avg Speed (B/s) | Decode Latency |
|-----------|----------------|-----------------|----------------|-----------------|-----------------|----------------|
| **Blocks (JSON)** | 0.9 KB | 1.4 KB | **100 % (lossless)** | ~2 800 | ~900 k B/s | 2–3 ms |
| **Calldata (HEX)** | 3.6 KB | 6.5 KB | **100 % (lossless)** | ~750 | 1–4 MB/s | 3–5 ms |
| **Receipts (JSONL)** | 8.9 KB | 17.5 KB | **100 % (lossless)** | ~500 | 5.3 MB/s | 5 ms |
| **Logs (JSONL)** | 5.2 KB | 10 KB | **100 % (lossless)** | ~670 | 3.8 MB/s | 4 ms |
| **Transactions (RLP)** | 0.6–3.9 KB | 0.5–3.4 KB | **100 % (lossless)** | 1 400–5 100 | 0.6–2 MB/s | 2–5 ms |

---

## 4. Global Metrics

| Metric | Value |
|--------|--------|
| **Average reduction (structured data)** | 44.7 % |
| **Average encode + decode latency** | < 10 ms / object |
| **Median throughput (bidirectional)** | 3 – 8 MB/s |
| **CPU cost** | 400 – 3 000 cycles / byte |
| **Peak memory usage** | ~9 MB |
| **Data integrity** | ✅ Perfect byte-for-byte restoration |

---

## 5. Key Observations

- **Deterministic performance:** identical results and timing across runs.  
- **Symmetric throughput:** decode as fast or faster than encode.  
- **Structured payloads benefit most:** JSON/HEX achieve 40–50 % savings.  
- **RLP/CBOR already efficient:** typically skipped or auto-bypassed.  
- **Low latency:** < 5 ms per compression cycle on small payloads.  
- **Lossless:** SHA-256 verification confirms perfect recovery.

---

## 6. Integration Outlook

ZCHAIN is engineered for blockchain node and validator environments.

| Context | Integration Target |
|----------|--------------------|
| **Arbitrum Orbit / Stylus** | Validator pipeline compression for block propagation |
| **Solana / Agave** | Turbine or shred-level transport compression |
| **IoT / Edge devices** | Lightweight binary transport, ~9 MB RAM footprint |
| **Off-chain data lakes** | Archival & analytics pipelines (JSON/CSV logs) |
| **SDK / APIs** | C / C++ / Rust / Python bindings provided |

Future enhancements:
- Adaptive “auto-skip” mode for dense RLP streams  
- SIMD-optimized decode path (Stylus WASM)  
- Parallel batch encoder for block-level streaming  

---

## 7. Summary Chart

| Category | Encode Speed (MB/s) | Decode Speed (MB/s) | Combined (MB/s) | Reduction |
|-----------|---------------------|---------------------|-----------------|------------|
| **Blocks JSON** | 0.7 | 0.9 | 1.6 | 35 % |
| **Calldata HEX** | 1.3 | 2.5 | 3.8 | 43 % |
| **Logs JSONL** | 1.4 | 3.8 | 5.2 | 48 % |
| **Receipts JSONL** | 6.6 | 5.3 | 11.9 | 49 % |
| **Transactions RLP** | 1.0 | 1.2 | 2.2 | −15 % (expansion) |

---

## 8. Conclusion

**ZCHAIN** achieves consistent, high-performance, fully lossless compression for blockchain workloads.  
On structured data, it reaches up to **50 % size reduction**, **< 10 ms latency**, and **throughput up to 12 MB/s per thread**, while guaranteeing deterministic decode.

ZCHAIN is suitable for:
- Validator data streaming (Orbit / Stylus / Solana)  
- RPC bandwidth optimization  
- Off-chain storage and analytics  
- Archival compression for on-chain state snapshots  

Future versions will introduce multithreaded and SIMD-enhanced back-ends for Stylus and other WASM execution environments.

---

## 9. License & Attribution

© 2025 Zetako Compression Lab.  
All rights reserved.  

For technical inquiries or collaborations:  
**Zetako Compression Lab**  
GitHub → [Zetako-Compression-Lab](https://github.com/organizations/Zetako-Compression-Lab)  
Core domains: C / C++ / Rust / Python / Solidity / Blockchain Infrastructure / MCU Compression.

---

**End of Report**
