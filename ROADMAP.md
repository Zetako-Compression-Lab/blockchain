# ZChain Public Engineering Roadmap

This roadmap tracks the public engineering priorities around ZChain's blockchain compression work. It is intentionally focused on **measurable codec progress, reproducible benchmarks and validated integrations** rather than speculative features.

## Current baseline

The current public generation sequence is:

- **v3** — historical compatibility baseline;
- **v4** — compatibility-oriented optimization, preserving the tested v3 compressed byte counts while materially improving encode throughput;
- **Speed_First** — higher-throughput profile for deployments where encoder and decoder can adopt a new bitstream together.

The current public benchmark corpus covers Ethereum/Reth, Solana mainnet RPC, Agave integration work and Cosmos/CometBFT payloads.

---

## Priority 1 — Codec performance

- continue encode-path optimization without sacrificing measured compression density;
- improve decode throughput, especially on read-heavy workloads;
- collect p50/p95/p99 latency distributions across representative payload sizes;
- add stable cycles/byte measurements on platforms where reliable counters are available;
- benchmark on representative x86_64 server CPUs in addition to Apple Silicon.

## Priority 2 — v4 compatibility validation

- grow the v3/v4 regression corpus;
- verify byte-for-byte compatibility across more payload classes;
- document framing/version behavior clearly;
- preserve compatibility claims only where directly supported by the regression corpus.

## Priority 3 — Speed_First production framing

- define explicit frame/profile identification;
- document encoder/decoder version negotiation;
- test mixed-version failure behavior;
- evaluate whether further speed gains are possible with minimal size impact;
- continue testing incompressible and adversarial payload behavior.

## Priority 4 — Ethereum / Reth

- add native RLP block and receipt payload benchmarks;
- compare JSON vs RLP vs Reth database representations;
- benchmark potential snapshot/export use cases;
- evaluate any future storage or networking hook independently from the standalone harness.

## Priority 5 — Solana / Agave

- expand Solana mainnet RPC distributions;
- collect representative shred-shadow measurements;
- isolate release-mode codec cost inside the Agave integration;
- benchmark on representative validator hardware;
- validate blockstore or transport experiments separately before production claims.

## Priority 6 — Cosmos / CometBFT

- expand height and payload diversity;
- add distribution-level reporting rather than selected examples only;
- evaluate potential RPC/export integration paths;
- separate codec benchmark savings from any future live node/network claim.

## Priority 7 — Comparative benchmarking

- keep codec comparisons workload-specific and reproducible;
- compare the same payloads and documented presets;
- report compressed size, encode speed and decode speed separately;
- avoid universal ranking claims from a single dataset.

---

## Public documentation policy

Every stronger public claim should be accompanied by:

1. dataset description;
2. exact build/profile;
3. hardware/OS context;
4. benchmark methodology;
5. round-trip/integrity result where applicable;
6. explicit boundary between native codec and integration-level performance.

See [README.md](README.md) for the current product evolution and [docs/README.md](docs/README.md) for blockchain-specific benchmark pages.
