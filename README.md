# alt.fun Token List

Automated [Uniswap Token List](https://github.com/Uniswap/token-lists) of **graduated**
[alt.fun](https://alt.fun) tokens tradable on Hyperswap (HyperEVM, chainId `999`).

> This repository contains **only the generated `tokenlist.json`**. It is produced and
> committed automatically by a scheduled indexer (separate repository) — **do not edit
> `tokenlist.json` by hand**, it will be overwritten on the next run.

## Consume it

```
https://raw.githubusercontent.com/HyperSwap-Labs/hyperswap-alt-fun-token-list/main/tokenlist.json
```

For high traffic, put a CDN / GitHub Pages in front — `raw.githubusercontent.com` has rate
limits and a few minutes of cache.

## What gets indexed

The indexer paginates the alt.fun public API for graduated tokens:

```
GET https://api.alt.fun/api/v1/tokens?limit=30&offset=0&status=graduated
```

It walks every page (`offset += 30`) until a page returns fewer than 30 items.

A token is included in the list **only if both**:

- `hyperswapPair` is present and non-empty (the token has a Hyperswap pair), **and**
- `imageUrl` is present and non-empty (the token has a logo).

Tokens missing either are skipped entirely.

## Where each field comes from

| Field      | Source                                                    |
|------------|-----------------------------------------------------------|
| `chainId`  | `999` (HyperEVM mainnet)                                   |
| `address`  | alt.fun `address`, checksummed (EIP-55)                   |
| `name`     | **on-chain** `name()`                                     |
| `symbol`   | **on-chain** `symbol()`                                   |
| `decimals` | **on-chain** `decimals()`                                 |
| `logoURI`  | `https://api.alt.fun` + alt.fun `imageUrl`                |

`name`, `symbol` and `decimals` are read **on-chain** from each ERC-20 contract (batched via
Multicall3 on HyperEVM), not taken from the API — so the list reflects the contract's truth.
A token whose on-chain read fails is excluded from that run.

## Format & update policy

- Standard [Uniswap Token List](https://github.com/Uniswap/token-lists) schema
  (`name`, `timestamp`, `version`, `keywords`, `tokens`).
- Tokens are sorted by address, so the file is deterministic.
- The indexer runs on a schedule and **only commits when the token set actually changes**;
  each change bumps `version.patch`.
- A safety guardrail refuses to publish an empty or drastically shrunken list (e.g. if the
  upstream API changes shape), to protect consumers from a broken feed.

## Example entry

```json
{
  "chainId": 999,
  "address": "0x0148b638Da5De885A3E2D89C5e3bf0C6D0000000",
  "name": "Hyperbald",
  "symbol": "BALD",
  "decimals": 18,
  "logoURI": "https://api.alt.fun/images/tokens/5e8d089a-...-hyperbald-banner.webp"
}
```
