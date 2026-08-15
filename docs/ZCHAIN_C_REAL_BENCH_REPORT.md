# ZChain C Microbenchmark Report

Date: 2026-08-15  
CPU: Apple M4  
OS: macOS Darwin 25.5.0 arm64

This report measures ZChain with a standalone C benchmark harness:

```text
zchain-c-bench/zchain_c_bench.c
```

The benchmark loads all input files before timing. The timed section contains only in-memory ZChain encode/decode work and round-trip validation. It excludes process startup, file reads, CSV writes, and SHA hashing.

## Build

```bash
cd zchain-c-bench
make clean && make
```

Build modes:

- `zchain_c_bench_debug`: `-O0 -g`
- `zchain_c_bench_release`: `-O3 -DNDEBUG -march=native`

## Dataset

Inputs:

- Agave report payload: `ZChain_Benchmark_Report.md`
- Agave/ZChain source payloads: `zchainv3-rs-full/src`
- Ethereum/Reth JSON-RPC payloads: `reth-zchain/tools/reth-zchain-bench/benchmark-data/ethereum-json`

Ethereum payloads include:

- 5 `eth_getBlockByNumber` full-transaction block JSON payloads
- 5 `eth_getBlockReceipts` JSON payloads

Total input: **18 files, 2,942,777 bytes**.

## Release C Results

Command:

```bash
./zchain_c_bench_release 100 \
  ../ZChain_Benchmark_Report.md \
  ../zchainv3-rs-full/src \
  ../reth-zchain/tools/reth-zchain-bench/benchmark-data/ethereum-json
```

| Dataset | Files | Input bytes | ZChain bytes | Savings | Iterations | Encode MiB/s | Decode MiB/s | Encode ns/byte | Decode ns/byte | Enc p50 ms | Enc p95 ms | Enc p99 ms | Dec p50 ms | Dec p95 ms | Dec p99 ms |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| all | 18 | 2,942,777 | 572,658 | 80.54% | 100 | **63.15** | **11.19** | 15.10 | 85.19 | 1.8363 | 12.5872 | 12.7425 | 1.9729 | 13.7680 | 14.2248 |
| agave_report_markdown | 1 | 6,000 | 2,887 | 51.88% | 100 | 27.08 | 12.72 | 35.22 | 74.95 | 0.2047 | 0.2878 | 0.3050 | 0.2169 | 0.2208 | 0.2236 |
| agave_zchain_sources | 7 | 28,270 | 10,537 | 62.73% | 100 | 32.03 | 11.89 | 29.77 | 80.24 | 0.1080 | 0.2170 | 0.2323 | 0.1083 | 0.2257 | 0.2281 |
| ethereum_block_json | 5 | 1,256,590 | 363,968 | 71.04% | 100 | 44.19 | 11.66 | 21.58 | 81.81 | 3.2275 | 12.7370 | 12.7945 | 3.5711 | 14.2195 | 14.2670 |
| ethereum_receipts_json | 5 | 1,651,917 | 195,266 | **88.18%** | 100 | **96.84** | 10.38 | 9.85 | 91.91 | 3.3401 | 4.7906 | 4.8101 | 3.7019 | 5.3422 | 5.3515 |

## Debug C Results

The same harness was also built with `-O0 -g`. Across the combined dataset, debug reached 26.40 MiB/s encode and 3.65 MiB/s decode. Debug builds are therefore not representative of ZChain release performance.

## Cargo Release Sanity Checks

Agave release command:

```bash
cd agave
./cargo run --release -p zchain-bench --features zchainv3 -- ../ZChain_Benchmark_Report.md
```

Observed:

- Input: 6,000 bytes
- ZChain: 2,887 bytes
- Savings: 51.88%
- Encode: 0.750 ms, 7.63 MiB/s
- Decode: 0.468 ms, 5.88 MiB/s
- Round trip: OK

Directory release command:

```bash
./cargo run --release -p zchain-bench-dir --features zchainv3 -- \
  ../zchainv3-rs-full/src \
  /tmp/zchain-src-bench-release.csv
```

Observed CSV confirms 7/7 source files round-trip OK.

## Cycles Per Byte

Cycles/byte is `NA` on this machine because Apple Silicon does not expose a stable user-space cycle counter or CPU frequency through `sysctl`.

The C harness supports an explicit frequency override:

```bash
CPU_FREQ_HZ=<hz> ./zchain_c_bench_release 100 ...
```

When set:

```text
cycles_per_byte = ns_per_byte * CPU_FREQ_HZ / 1e9
```

## Interpretation

The standalone C release harness is the current **native codec speed baseline** because it isolates in-memory ZChain encode/decode work from process startup, file I/O, CSV output, SHA hashing, and higher-level integration overhead.

Current measured baseline on the Apple M4 test system:

- **63.15 MiB/s overall encode**
- **11.19 MiB/s overall decode**
- **80.54% overall savings**
- **96.84 MiB/s encode on Ethereum receipts with 88.18% savings**
- **44.19 MiB/s encode on Ethereum full block JSON with 71.04% savings**

The decode path is currently materially slower than encode and is the most obvious performance optimization target.

## Public reporting rule

Native codec performance and integration-level performance must be reported separately. Agave development-harness throughput must not be presented as ZChain codec throughput. Release-mode integration benchmarks may be published when their scope and overhead are explicit.
