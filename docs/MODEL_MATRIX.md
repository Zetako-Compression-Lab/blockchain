# ZChain Model Matrix

[← Product evolution](../README.md) · [Methodology](METHODOLOGY.md)

The public registry distinguishes a **real specialized model** from a model name that merely describes a future integration surface.

## Maturity labels

- **SUPPORTED** — real-corpus evidence + explicit integration contract.
- **ADAPTER_READY** — serialization boundary defined; specialized model and/or chain-specific benchmark still pending.
- **RESEARCH** — architecture or model still being explored.

## Current matrix

| Chain / family | Serialization / surface | Maturity | Current direction | Page |
|---|---|---|---|---|
| Ethereum / Reth | JSON-RPC hex | **SUPPORTED** | negotiated `ETHEREUM_HEX` | [Ethereum](ETHEREUM.md) |
| Ethereum / EVM | native RLP | **RESEARCH** | schema-assisted RLP, opaque crypto, references | [Ethereum](ETHEREUM.md) |
| Base / Arbitrum / Optimism / Polygon / BNB / Avalanche | EVM JSON-RPC | **SUPPORTED model / chain bench pending** | reuse Ethereum hex family until data justifies divergence | [EVM L2](EVM_L2.md) |
| Solana | JSON-RPC | **SUPPORTED** | structured RPC profile | [Solana](SOLANA.md) |
| Agave | shred / entry / validator paths | **RESEARCH** | schema writer + shadow measurement | [Agave](AGAVE.md) |
| CometBFT | JSON-RPC | **SUPPORTED** | block / results / commit / validator payloads | [Cosmos / CometBFT](COSMOS_COMETBFT.md) |
| Cosmos SDK | protobuf | **ADAPTER_READY** | tags/varints structural; signatures/hashes opaque | [Cosmos / CometBFT](COSMOS_COMETBFT.md) |
| Bitcoin | raw block / tx / witness | **ADAPTER_READY** | headers + compact integers structural; crypto opaque | [Bitcoin](BITCOIN.md) |
| Polkadot / Substrate | SCALE | **ADAPTER_READY** | compact integer + hash/signature sidecars | [Substrate](SUBSTRATE.md) |
| Cardano | CBOR | **ADAPTER_READY** | CBOR structure + crypto separation | [Cardano](CARDANO.md) |
| Sui | BCS / RPC | **ADAPTER_READY** | BCS-aware structural model | [Sui](SUI.md) |
| Aptos | BCS / RPC | **ADAPTER_READY** | BCS-aware structural model | [Aptos](APTOS.md) |
| TRON | block / transaction payloads | **ADAPTER_READY** | structured fields + address/signature separation | [TRON](TRON.md) |
| generic snapshots | key/value / state chunks | **ADAPTER_READY** | per-chain locality models | — |

## Why models follow serialization families

ZChain does not need one marketing codec name per chain if several chains expose the same serialization behavior. A new model earns its own implementation when a real benchmark shows that the data distribution or serialization family is materially different.

Examples:

- EVM-compatible JSON can share the Ethereum hex family;
- Sui and Aptos both use BCS concepts, but chain-specific payload distributions may still justify different tuning later;
- Cosmos JSON-RPC and Cosmos protobuf are different workloads and should not share performance claims;
- Agave validator structures are different from Solana JSON-RPC, even though they belong to the same ecosystem.

## Promotion policy

An `ADAPTER_READY` model becomes `SUPPORTED` only after a real corpus, exact round trip, optimized native benchmark and public methodology are available. Until then, its page describes **what will be measured**, not fictional performance.
