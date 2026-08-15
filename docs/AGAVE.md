# Agave — ZChain Validator Integration

[← Product evolution](../README.md) · [Solana data](SOLANA.md) · [Methodology](METHODOLOGY.md)

Agave is where ZChain is evaluated as a **validator-side integration technology**, not just as a codec running on exported Solana JSON.

## Why Agave is different from the Solana benchmark page

The Solana page asks: **how well does ZChain compress real Solana payloads?**

The Agave page asks: **where can compression be inserted safely inside a latency-sensitive validator architecture, and how do we prove that the CPU cost is justified?**

## Integration surfaces

The public integration work includes:

| Surface | Purpose |
|---|---|
| codec adapter / trait | isolate ZChain from validator-facing code |
| ledger compression shim | fail-open integration and metrics |
| benchmark tools | reproducible round-trip tests |
| shred shadow probe | measure real shred payloads without changing wire behavior |
| future schema writer | allow the runtime to identify signatures, blockhashes and account keys directly |

## Fail-open principle

Compression is initially an optimization, not a validator correctness dependency.

```text
Agave payload
     │
     ├──────────────> normal path
     │
     └─> ZChain shadow / experimental path
              │
              ├─ success -> metrics + exact round trip
              └─ failure -> original payload remains available
```

This lets ZChain collect evidence before any network or storage format is changed.

## Latest ledger-source result

The latest 1.2.0 Research reference run includes the `solana-shred` profile on an Agave ledger-source corpus.

Apple M4, native C release, `-O3 -DNDEBUG -mcpu=native`, 50 iterations:

| Files | Raw bytes | ZChain bytes | Savings | Encode | Decode | Encode ns/B | Decode ns/B |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 32 | 879,258 | **222,165** | **74.73%** | **81.81 MiB/s** | **54.38 MiB/s** | 11.657 | 17.537 |

All benchmarked files round-trip exactly and the package passed the test and sanitizer gates.

This result is **ledger-source evidence**, not a claim about live validator wire traffic or production shred transport.

[Open the full 1.2.0 M4 benchmark report →](ZCHAIN_BLOCKCHAIN_C_1_2_0_M4_BENCH_REPORT.md)

## Why schema assistance matters on Solana

Agave already knows important field identity while building transactions and ledger structures. A specialized ZChain path can use that knowledge:

- signatures → opaque;
- recent blockhash → referenceable / known reference;
- account keys → opaque or referenceable;
- repeated arrays of keys → reference runs;
- compact metadata / instruction indexes → structural model.

The important architectural rule is **no global content scan**. The validator tells ZChain what each segment represents.

## Current evidence

The Agave work now establishes:

- successful codec calls through Agave-facing code;
- exact round trips;
- fail-open integration behavior;
- a shadow measurement path for shred data;
- a measured ledger-source corpus under the 1.2.0 Research engine.

The original non-release Agave throughput figures are not used as native codec claims. Native Solana RPC performance is documented separately on the [Solana page](SOLANA.md).

## What would move Agave toward production readiness

A stronger validator claim requires:

1. representative live-path shred / entry / blockstore datasets;
2. release-mode end-to-end timings including adapter cost;
3. p50 / p95 / p99 latency;
4. CPU and memory cost per validator worker;
5. end-to-end failure testing;
6. exact compatibility behavior for any enabled storage or transport path;
7. a measured decision showing that byte savings justify added CPU on that path.

Until then, Agave remains an **integration and measurement program**, with current ledger-source evidence but no production validator-traffic claim.

Detailed engineering evidence remains in [AGAVE_INTEGRATION.md](AGAVE_INTEGRATION.md).
