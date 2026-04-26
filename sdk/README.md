# @prova-network/sdk

A TypeScript SDK for interacting with [Prova](https://prova.network) — verifiable storage anchored to Ethereum, settled on Base in USDC.

> **Forked from [`FilOzone/synapse-sdk`](https://github.com/FilOzone/synapse-sdk)** under the Permissive License Stack (Apache-2.0 OR MIT). Prova elects MIT. See [`ATTRIBUTION.md`](../ATTRIBUTION.md) at the repo root for the full upstream credit and the deltas.

## Install

```bash
npm install @prova-network/sdk viem
# or
pnpm install @prova-network/sdk viem
```

`viem` is a peer dependency. The SDK is `viem@2.x`-only.

## Quickstart

```ts
import { createWalletClient, http } from 'viem'
import { privateKeyToAccount } from 'viem/accounts'
import { base } from 'viem/chains'
import { Prova } from '@prova-network/sdk'

const account = privateKeyToAccount(process.env.PRIVATE_KEY as `0x${string}`)
const client = createWalletClient({
  account,
  chain: base,
  transport: http(),
})

const prova = await Prova.create({ client })

// Upload bytes -> get a piece-CID + deal id
const file = await fetch('https://example.com/dist.tar.gz').then(r => r.arrayBuffer())
const { pieceCid, dealId } = await prova.storage.upload(new Uint8Array(file))

console.log({ pieceCid, dealId })

// Later: retrieve and verify locally
const bytes = await prova.storage.retrieve(pieceCid)
// Bytes are auto-verified against the on-chain piece-CID by default.
```

## Concepts

| Term | What it is |
| --- | --- |
| **Prova** | The high-level facade. Wires up payments + storage + sp-registry against a single chain + RPC. Use this unless you need fine-grained control. |
| **Piece-CID** | Content commitment for a file. The same value Filecoin's Curio computes — Prova reuses the algorithm so a Filecoin SP and a Prova prover talk about the same identifier for the same bytes. |
| **Deal** | An on-chain row in `StorageMarketplace` linking a piece-CID, a payer, one or more provers, and a payment stream. |
| **PDP** | Provable Data Possession — the cryptographic protocol that lets a verifier (the on-chain `ProofVerifier`) check a prover still has the bytes without downloading them. |
| **Service Provider Registry** (`sp-registry`) | The on-chain registry of provers. Internally still named `sp-registry` to match upstream Filecoin terminology and on-chain ABI; will be renamed to `prover-registry` in a coordinated cross-package sweep. |

## Modules

The SDK is split into a small number of high-cohesion modules. Most consumers use the `Prova` facade and never touch the modules directly.

```ts
import { Prova } from '@prova-network/sdk'                      // facade
import { PaymentsService } from '@prova-network/sdk/payments'    // USDC payment rails
import { StorageService } from '@prova-network/sdk/storage'      // upload / retrieve
import { SPRegistryService } from '@prova-network/sdk/sp-registry' // active provers
import { MarketplaceService } from '@prova-network/sdk/marketplace' // deal creation, fees
```

See the per-module READMEs and the [docs site](https://docs.prova.network/sdk/) for full API references.

## Status

This package is **in development** and tracking the active pivot to the Ethereum-native v2 architecture. Expect:

- The public surface to firm up before the v0.1.0 release.
- Method names and module boundaries to change as we shed Filecoin-specific terminology (notably `sp-registry` → `prover-registry`, `WarmStorage` → `Marketplace`).
- The upstream `@filoz/synapse-*` packages remain useful references; we deliberately keep our directory structure close to upstream for now to make merging upstream bug fixes easier.

## Compared to upstream `@filoz/synapse-sdk`

What changed from upstream:

- **Chain**: Filecoin Mainnet/Calibration → Base Mainnet/Sepolia.
- **Payment token**: USDFC → USDC (Base-native, `0x833589fcd6edb6e08f4c7c32d4f71b54bda02913`).
- **Service contracts**: `WarmStorage` family → `StorageMarketplace` + `ProverRegistry` + `ProofVerifier` family on Base.
- **Random beacon**: Filecoin DRAND → `block.prevrandao` on Base, optionally Chainlink VRF (configurable per-deployment).
- **Identity**: addresses instead of Filecoin actor IDs throughout.
- **No Filecoin-specific features**: removed Calibration testnet wiring, `f1`/`f4` address translation, FIL precision helpers.

What stayed:

- Piece-CID / CommP computation (algorithm is chain-agnostic, MIT-clean).
- Merkle proof verification helpers.
- Streaming retrieval pipeline with progressive verification.
- Test fixture format and generation utilities.

## License

Dual-licensed under [MIT](../LICENSE-MIT) or [Apache-2.0](../LICENSE-APACHE), at your option. Forked components retain upstream attribution per [`ATTRIBUTION.md`](../ATTRIBUTION.md).

## Contributing

Issues and PRs welcome at [`github.com/prova-network/sdk`](https://github.com/prova-network/sdk). For non-trivial changes, please open an issue first; for typo / link / comment fixes, just send the PR. See the umbrella repo's [`CONTRIBUTING.md`](https://github.com/prova-network/prova/blob/main/CONTRIBUTING.md) for the project-wide guidelines.
