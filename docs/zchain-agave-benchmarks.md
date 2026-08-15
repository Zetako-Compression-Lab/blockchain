# ZChain Agave Compression Benchmarks

This note documents the ZChain v3 integration points added to Agave and the benchmark commands used to produce reproducible public metrics.

## Integration Points

- `compression/api`: small codec trait used by Agave-facing code.
- `compression/zchainv3`: ZChain v3 Rust codec adapter.
- `ledger/src/compression.rs`: fail-open compression shims and benchmark metrics.
- `tools/zchain-bench`: single-file round-trip benchmark.
- `tools/zchain-bench-dir`: directory benchmark that emits CSV.
- `ledger/src/zc_shadow.rs`: optional shadow probe for measuring shred payload compression without changing the wire format.

## Build

```bash
cd agave
./cargo check -p solana-ledger --features zchainv3
./cargo check -p zchain-bench --features zchainv3
./cargo check -p zchain-bench-dir --features zchainv3
```

## Single-File Benchmark

```bash
./cargo run -p zchain-bench --features zchainv3 -- ../ZChain_Benchmark_Report.md
```

Observed locally on 2026-08-15:

| Input | Raw bytes | ZChain bytes | Ratio | Savings | Round trip |
| --- | ---: | ---: | ---: | ---: | --- |
| `ZChain_Benchmark_Report.md` | 6,000 | 2,887 | 0.4812 | 51.88% | SHA-256 OK |

## Directory Benchmark

```bash
./cargo run -p zchain-bench-dir --features zchainv3 -- ../zchainv3-rs-full/src /tmp/zchain-src-bench.csv
```

Observed local CSV sample:

| File | Raw bytes | ZChain bytes | Ratio | Savings | Round trip |
| --- | ---: | ---: | ---: | ---: | --- |
| `arithmetic.rs` | 3,639 | 1,337 | 0.3674 | 63.26% | OK |
| `bijection.rs` | 1,830 | 615 | 0.3361 | 66.39% | OK |
| `buffer.rs` | 2,389 | 978 | 0.4094 | 59.06% | OK |
| `codec.rs` | 6,356 | 1,778 | 0.2797 | 72.03% | OK |
| `main.rs` | 8,372 | 3,137 | 0.3747 | 62.53% | OK |
| `probability.rs` | 1,887 | 961 | 0.5093 | 49.07% | OK |
| `utils.rs` | 3,339 | 1,683 | 0.5040 | 49.60% | OK |

## Performance Publication Policy

The development-build timing and throughput values from the initial Agave port are intentionally **not published as ZChain performance figures**. They were collected through non-release benchmark commands and are not representative of the optimized codec target.

The next performance publication pass should use:

```bash
./cargo run --release -p zchain-bench --features zchainv3 -- ../ZChain_Benchmark_Report.md
./cargo run --release -p zchain-bench-dir --features zchainv3 -- ../zchainv3-rs-full/src /tmp/zchain-src-bench-release.csv
```

Representative validator hardware should be recorded together with release build settings, payload sizes, iteration counts, encode/decode throughput, latency distributions, and round-trip integrity.

## Public Use Cases

1. **Shred payload shadow measurements** — enable `zc_shadow` to measure raw-vs-ZChain compression on ledger shred payloads without changing network behavior.
2. **Blockstore storage experiments** — use the fail-open ledger shims to compare compressed payload size against raw payload size before enabling any write path.
3. **Validator log and JSON payload compression** — use `zchain-bench-dir` against captured validator artifacts and publish CSVs with ratio, savings, and round-trip status. Publish performance separately only from validated release-mode runs.

## Notes

- Current code is fail-open by design: compression errors return the original bytes instead of failing validator paths.
- The codec emits warnings in the current port (`unused_imports` and `static_mut_refs`). They do not block the benchmark commands above, but should be cleaned before treating the integration as production-ready.
- Current public compression figures are local development integration measurements. **No public ZChain speed claim is made from the initial dev-build timing data.**
