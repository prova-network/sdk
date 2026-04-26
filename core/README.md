# @prova-network/core

Low-level primitives for [Prova](https://prova.network) — verifiable storage anchored to Ethereum, settled on Base.

This package exposes the chain-agnostic building blocks that the higher-level [`@prova-network/sdk`](../sdk) is composed of: piece-CID computation, ABI bindings, payment-rail helpers, and the on-chain registry adapters.

> **Forked from [`FilOzone/synapse-sdk`](https://github.com/FilOzone/synapse-sdk)** under the Permissive License Stack (Apache-2.0 OR MIT). Prova elects MIT. See [`ATTRIBUTION.md`](../ATTRIBUTION.md) at the repo root for the full upstream credit and the deltas.

## Install

```bash
npm install @prova-network/core viem
# or
pnpm install @prova-network/core viem
```

`viem` is a peer dependency and must be installed separately. `viem@2.x` only.

## When to use this directly

- You're building a non-`Prova` facade and need direct access to a single contract (the `Prova` class in `@prova-network/sdk` already wires everything up, so prefer it for typical apps).
- You want to compute piece-CIDs without holding a payments context.
- You're writing a custom prover or aggregator and need the same encoding helpers Prova clients use.

```ts
import { computePieceCid } from '@prova-network/core/piece'
import { provaContracts } from '@prova-network/core/contracts'
import { base } from '@prova-network/core/chains'

const cid = await computePieceCid(bytes)
const { storageMarketplace } = provaContracts(base)
```

## Modules

| Module | What's in it |
| --- | --- |
| `@prova-network/core/chains` | Chain configuration (Base mainnet, Base Sepolia). |
| `@prova-network/core/contracts` | Generated ABIs and per-chain contract address maps. |
| `@prova-network/core/piece` | Piece-CID / CommP computation, Fr32 padding, Merkle helpers. |
| `@prova-network/core/marketplace` | StorageMarketplace types and rate calculation. |
| `@prova-network/core/sp-registry` | Service Provider Registry types and queries. |
| `@prova-network/core/utils` | Generic helpers (epoch math, formatUnits/parseUnits passthroughs from viem). |

The on-chain ABIs in `core/contracts` are generated via wagmi from the Solidity source in [`prova-network/contracts`](https://github.com/prova-network/contracts). Regenerate with `pnpm wagmi`.

## Status

This package is **in development** and the public surface is not yet stable. We deliberately keep the directory layout close to the upstream `@filoz/synapse-core` to make merging upstream bug fixes painless; the cosmetic rename pass to fully Prova-native naming will land in a coordinated sweep before v0.1.0.

## License

Dual-licensed under [MIT](../LICENSE-MIT) or [Apache-2.0](../LICENSE-APACHE), at your option. See [`ATTRIBUTION.md`](../ATTRIBUTION.md) for upstream attribution.

## Contributing

Issues and PRs welcome at [`github.com/prova-network/sdk`](https://github.com/prova-network/sdk). For non-trivial changes, please open an issue first.
