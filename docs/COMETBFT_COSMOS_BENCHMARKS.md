# ZChain Benchmarks for CometBFT / Cosmos Payloads

This document describes the public CometBFT / Cosmos Hub RPC payload corpus used by the ZChain C benchmark harness.

## Dataset

For each selected height, the fetcher collects:

- `block`
- `block_results`
- `commit`
- `validators`
- `consensus_params`

These payloads are useful because they represent several different types of structured consensus and execution metadata rather than one homogeneous JSON shape.

## v4 and Speed_First results

| Workload | v4 encode MiB/s | v4 decode MiB/s | v4 savings | Speed_First encode MiB/s | Speed_First decode MiB/s | Size delta vs v4 |
|---|---:|---:|---:|---:|---:|---:|
| `block` JSON | 49.72 | 31.47 | 52.66% | 58.18 | 34.19 | **-1.32%** |
| `block_results` JSON | **120.74** | **77.23** | **84.87%** | **143.42** | **85.77** | +1.03% |
| `commit` JSON | 52.20 | 31.40 | 52.75% | 57.64 | 34.05 | **-1.12%** |
| `consensus_params` JSON | 39.58 | 29.95 | 28.50% | 44.81 | 25.94 | +2.10% |
| `validators` JSON | 56.07 | 39.64 | 56.90% | 63.34 | 35.16 | +1.02% |

## Interpretation

The most compelling CometBFT workload in the current corpus is `block_results`:

- v4: **84.87% savings**, **120.74 MiB/s encode**, **77.23 MiB/s decode**
- Speed_First: **143.42 MiB/s encode**, **85.77 MiB/s decode**

Speed_First does not always increase output size. On `block` and `commit`, the measured compressed output is slightly smaller than v4.

## Reproduce

The dataset fetcher lives under the public benchmark tooling and can be pointed at another CometBFT endpoint using `COMETBFT_RPC_URL`.

The C harness reports ratio, savings, encode/decode MiB/s, ns/byte, latency distribution, and round-trip status.

## Public interpretation

These figures demonstrate workload-specific ZChain behavior on real CometBFT RPC data. They do not imply that CometBFT network traffic or storage would shrink by the same percentages without a separately validated integration path.