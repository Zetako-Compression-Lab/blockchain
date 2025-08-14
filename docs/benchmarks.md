# Benchmarks

This document reports compression **ratio, throughput, and memory** metrics for the Zetako engine on public, reproducible datasets.  
All tests are **lossless** and **byte-for-byte reversible**.

> For CSV data and raw logs, see `/benchmarks/`.  
> For reproduction scripts, see `/scripts/`.

---

## Summary (Current Snapshot)

| Dataset / Batch                              | Original Size | Compressed Size | Reduction | Encode Speed | Decode Speed |
|----------------------------------------------|--------------:|----------------:|----------:|-------------:|-------------:|
| **Solana Block JSON (sample, ~1.0 MB)**      | 1,049,233 B   | 344,925 B       | **67.13%**| 78 MB/s      | 73 MB/s      |
| **Avalanche Snapshot (C‑Chain, 13.14 MB)**   | 13.14 MB      | 2.56 MB         | **80.55%**| 81.75 MB/s   | 58.57 MB/s   |
| **Ethereum RLP Batch (representative)**      | 4.80 MB       | 2.01 MB         | **58.10%**| 72 MB/s      | 69 MB/s      |

> Notes:
> - Sizes are post‑compression payload sizes; ratios are relative to the raw input.  
> - Throughput is measured at the file level (end‑to‑end encode/decode), not per‑stage.  
> - Full per‑file results are provided as CSV in `/benchmarks/`.

---

## Methodology

### Test Harness
- Each run processes the **exact raw payload** (JSON/Base64/Binary/RLP) with Zetako CLI.
- Timing includes file I/O and end‑to‑end codec work (no warm cache assumptions).
- Each case is executed **3×**; we report the median.
- Decoding validates bit‑exact equality with the original input.

### Environment
- CPU: Apple M‑series class (ARM64)  
- RAM: 24 GB  
- OS: macOS 14+  
- Storage: NVMe SSD  
- Zetako: current private build (see `/benchmarks/VERSION.txt`)

> If you run on different hardware/OS, absolute speeds will change; **ratios** should be similar for the same dataset.

### Settings
- Mode: `lossless` (default)  
- Threads: `auto` (equals physical cores unless limited)  
- Dictionaries: none (no external state)  
- Block size / internal parameters: proprietary defaults

---

## Datasets

We use only **publicly accessible** or **derived-from-public** payloads:

- **Solana**
  - Block JSON exports and Base64 transaction/shred dumps
  - Sources: public RPC endpoints / historical archives
- **Ethereum / EVM**
  - RLP block batches, receipts, logs
  - Sources: public RPC endpoints (eth_getBlockByNumber etc.)
- **Avalanche (C‑Chain)**
  - Single blocks and snapshot slices from public RPC

> We do **not** redistribute the raw datasets here.  
> Use `/scripts/fetch_datasets.py` to download from their public sources or substitute your own.

---

## Reproduction

### 1) Prepare
```bash
# From repo root
python3 -m venv .venv && source .venv/bin/activate
pip install -r scripts/requirements.txt

# (Optional) Fetch public datasets
python scripts/fetch_datasets.py --target ./data
2) Run Benchmarks
bash
Copier
Modifier
# Encode
./bin/zetako-macos-arm64 encode \
  --input ./data/solana/block_001.json \
  --output ./out/block_001.zcaps \
  --log ./out/block_001.encode.log

# Decode (verification)
./bin/zetako-macos-arm64 decode \
  --input ./out/block_001.zcaps \
  --output ./out/block_001.roundtrip.json \
  --log ./out/block_001.decode.log

# Compare
python scripts/analyze_results.py \
  --original ./data/solana/block_001.json \
  --roundtrip ./out/block_001.roundtrip.json \
  --csv ./benchmarks/solana-blocks.csv
3) Aggregate & Plot
bash
Copier
Modifier
# Aggregate CSVs and emit summary tables + PNG charts in /benchmarks/graphs
python scripts/analyze_results.py --aggregate ./benchmarks --charts ./benchmarks/graphs
CSV Schemas
/benchmarks/solana-blocks.csv
javascript
Copier
Modifier
file,original_bytes,compressed_bytes,ratio_pct,encode_ms,decode_ms,encode_MBps,decode_MBps,ram_peak_MB,notes
block_001.json,1049233,344925,67.13,13.4,14.7,78.1,73.3,17.0,sample JSON with Base64 tx
...
/benchmarks/ethereum-blocks.csv
python-repl
Copier
Modifier
file,original_bytes,compressed_bytes,ratio_pct,encode_ms,decode_ms,encode_MBps,decode_MBps,ram_peak_MB,notes
batch_0001.rlp,5033164,2107638,58.10,66.8,69.6,72.0,69.1,16.2,rlp batch
...
/benchmarks/avalanche-blocks.csv
python-repl
Copier
Modifier
file,original_bytes,compressed_bytes,ratio_pct,encode_ms,decode_ms,encode_MBps,decode_MBps,ram_peak_MB,notes
snapshot.json,13774131,2684355,80.55,160.7,224.4,81.75,58.57,8.8-17.0,c-chain snapshot slice
...
Interpreting Results
Ratio (Reduction %): Sensitive to dataset structure.

Repetitive keys/addresses/signatures yield higher reductions.

Mixed binary and text (e.g., Base64 blobs inside JSON) benefit from structure-aware preprocessing.

Throughput:

Scales with core count; IO and filesystem caching can dominate for very small files.

Embedded targets show lower absolute MB/s but similar ratio performance.

Memory:

Zetako does not rely on external dictionaries; RAM scales with internal staging buffers and dataset size.

Optional Baselines (If You Choose To Include)
If you want to show baselines, populate /benchmarks/baselines/*.csv and reference them here.
Typical comparisons: LZ4, Snappy, Zstandard at default levels on the same files, measured with the same harness.

Keep in mind: “generic” codecs are not structure-aware; lower ratios are expected on blockchain payloads.

Known Limitations (Benchmarking Only)
Public RPC payloads can vary over time; pin exact block heights/hashes for strict reproducibility.

Extremely small files (< 32 KB) make throughput noisy due to process startup and IO overhead.

When running inside VMs/containers, CPU governor and IO throttling can skew numbers.

Updating This Report
Add new CSV rows to the relevant file under /benchmarks/.

Re-run analyze_results.py --aggregate.

Commit the updated CSVs and charts under /benchmarks/graphs.

Append a short note in CHANGELOG.md under the current version.

