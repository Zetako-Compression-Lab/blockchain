# ZChain Blockchain C 1.2.0 Research - M4 Bench Report

Date: 2026-08-15
CPU: Apple M4
Build: native C release, `-O3 -DNDEBUG -mcpu=native`
Version: `zchain-blockchain-c/1.2.0-research`

## Validation

- `make test`: pass
- `make sanitize ASAN_OPTIONS=detect_leaks=0:halt_on_error=1`: pass
- All benchmarked files round-trip exactly.
- `ETHEREUM_SCHEMA` smoke example emits `encoding=3`.

## Supported Corpus Results

| Corpus | Payload | Files | Iterations | Raw bytes | ZChain bytes | Savings | Encode | Decode | Encode ns/B | Decode ns/B |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Ethereum Reth JSON | `ethereum-hex` | 10 | 50 | 2,908,507 | 512,808 | 82.37% | 131.11 MiB/s | 114.69 MiB/s | 7.274 | 8.315 |
| Ethereum Reth JSON | `ethereum-block` | 10 | 50 | 2,908,507 | 559,234 | 80.77% | 91.83 MiB/s | 57.70 MiB/s | 10.386 | 16.528 |
| Solana mainnet RPC JSON | `solana-rpc` | 21 | 30 | 26,106,696 | 4,493,986 | 82.79% | 110.40 MiB/s | 67.93 MiB/s | 8.638 | 14.039 |
| CometBFT CosmosHub RPC JSON | `cometbft` | 25 | 50 | 968,332 | 295,048 | 69.53% | 67.29 MiB/s | 43.19 MiB/s | 14.173 | 22.081 |
| Agave ledger source | `solana-shred` | 32 | 50 | 879,258 | 222,165 | 74.73% | 81.81 MiB/s | 54.38 MiB/s | 11.657 | 17.537 |

## Ethereum Hex Delta

Against the older `ethereum-block` path on the same Ethereum JSON corpus:

- Size: 512,808 B vs 559,234 B, so `ethereum-hex` is 8.30% smaller.
- Encode: 131.11 MiB/s vs 91.83 MiB/s, so `ethereum-hex` is 1.43x faster.
- Decode: 114.69 MiB/s vs 57.70 MiB/s, so `ethereum-hex` is 1.99x faster.

## Ethereum Schema Status

`ETHEREUM_SCHEMA` was not benchmarked as a supported profile in this cycle.

Reason: it still needs real Reth/Alloy RLP bytes plus runtime-provided spans. The current smoke only verifies that the new explicit stream writer emits `encoding=3` and passes sanitizer-covered round-trip tests.

Smoke output:

```text
encoding=3 frame=2430
```

## Files

- `zchain-1.2.0-ethereum-hex-m4-50.csv`
- `zchain-1.2.0-ethereum-block-m4-50.csv`
- `zchain-1.2.0-solana-rpc-m4-30.csv`
- `zchain-1.2.0-cometbft-rpc-m4-50.csv`
- `zchain-1.2.0-agave-ledger-src-m4-50.csv`
- `zchain-1.2.0-ethereum-schema-smoke-m4.txt`
