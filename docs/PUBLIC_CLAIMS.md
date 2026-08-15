# ZChain Public Claims

This file defines what the current public Agave evidence supports and what should remain explicitly out of scope until stronger validation exists.

## Safe claims

| Claim | Status | Scope |
|---|---|---|
| Agave-facing ZChain v3 integration builds | Verified | `solana-ledger`, `zchain-bench`, and `zchain-bench-dir` pass the documented `cargo check` commands with the `zchainv3` feature |
| Single-file benchmark tooling | Verified | `tools/zchain-bench` |
| Directory CSV benchmark tooling | Verified | `tools/zchain-bench-dir` |
| SHA-256 round-trip verification | Verified in documented benchmark runs | Reported by benchmark path |
| Fail-open behavior | Verified by integration design | Compression errors return original bytes instead of failing validator paths |
| Shred shadow measurement path | Implemented as optional probe | Measures shred payload compression without changing the wire format |
| Single-file savings | Measured locally | 6,000 B → 2,887 B, 51.88% savings, SHA-256 OK on `ZChain_Benchmark_Report.md` |
| Directory sample savings | Measured locally | 49.07% to 72.03% across seven listed Rust source files |
| Directory sample round trip | Verified in documented rows | All seven listed rows report OK |
| Development encode/decode throughput | Measured locally | Per-file MiB/s values in the public benchmark note |

## Claims to avoid for now

- "Production-ready Agave integration."
- "Production validator throughput."
- "Mainnet bandwidth reduction of X%."
- "Shred traffic savings of X%" until real shred-shadow datasets are collected and published.
- "Release-optimized performance" until the benchmark commands are rerun with `--release`.
- "Wire-format compatible compressed transport" because the documented shadow path does not change the wire format.
- "Zero-risk integration" or similar absolute reliability wording.

## Current technical caveats

The current port still emits:

- `unused_imports`
- `static_mut_refs`

They do not block the documented benchmark commands, but they should be cleaned before describing the integration as production-ready.

## Public wording candidates

- **ZChain has a validated Agave development integration with fail-open compression shims, reproducible round-trip benchmarks, and an optional shred shadow probe.**
- **A documented local benchmark compressed a 6,000-byte input to 2,887 bytes, saving 51.88%, with SHA-256 round-trip verification.**
- **In a seven-file local directory sample, observed savings ranged from 49.07% to 72.03%, with every listed row reporting successful round trip.**
- **The shred shadow probe is designed to measure ZChain behavior without changing Agave's network wire format.**
- **Current figures are development measurements; release-mode and target-hardware benchmarks are still required before production performance claims.**

## Required before stronger claims

- clean the current Rust warnings;
- benchmark with `--release`;
- benchmark on representative validator hardware;
- collect shred-shadow measurements on representative ledger traffic;
- publish distributions, not only selected examples, for any future network or storage savings claim;
- separately validate any future enabled compressed write/transport path.
