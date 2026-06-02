---
title: 'Polling Avalanche Wallet Balances at Scale: A Guide for Custodians'
url: https://bitquery.io/blog/polling-avalanche-wallet-balances-at-scale
date: '2026-05-27'
author: ''
feed_url: https://bitquery.io/feed
---
A custodial [Avalanche](https://bitquery.io/blockchains/avalanche-api?utm_source=blog&utm_campaign=avalanche_balances) product, a treasury management tool, or a multi-wallet portfolio service all have the same data problem. The product needs the AVAX balance and ERC-20 token holdings for every customer address it tracks, anchored to a specific block height for deterministic reporting, refreshed on a clock so the UI is never stale, across thousands of addresses without operating an Avalanche archive node and without paying per call for repeated polls.

This article walks through the [Avalanche address balances query](https://docs.bitquery.io/v1/docs/Schema/Avalanche/address?utm_source=blog&utm_campaign=avalanche_balances) pattern, the block-height anchoring step that makes snapshots reproducible, the caching behaviour that cuts the cost of most repeat polls to near zero, the single-address-versus-batched trade-off, and the buy-versus-build comparison against a self-hosted Avalanche archive node plus indexer.

It is the Avalanche companion to the [Cardano polling guide](/blog/polling-cardano-stake-and-reward-balances-at-scale) for custodians. Same pattern, different chain, different schema namespace.

## Avalanche Balance Query at a Pinned Block Height

The query a custodial Avalanche product needs returns the AVAX balance and every ERC-20 holding for one address, anchored at a chosen block height. Open the [Avalanche balances at block height query in the Bitquery GraphQL IDE](https://ide.bitquery.io/avalanche-address-balances-at-height?utm_source=blog&utm_campaign=avalanche_balances) to run and modify it against any address.

```
{
  ethereum(network: avalanche) {
    blocks(height: {is: 60000000}) {
      height
      timestamp {
        time(format: "%Y-%m-%dT%H:%M:%SZ")
      }
    }
    address(address: {is: "0x794a61358D6845594F94dc1DB02A252b5b4814aD"}) {
      balances(height: {lteq: 60000000}) {
        value
        currency {
          address
          symbol
          name
          decimals
          tokenType
        }
      }
    }
  }
}
```

Swap the `is:` address for any Avalanche wallet. Swap the `height` value for any block you want the snapshot anchored at.

The response carries two halves. The `blocks` field confirms the chosen height exists and returns the wall-clock timestamp of that block, which becomes the snapshot's "as of" stamp on user-facing reports. The `address.balances` field returns the AVAX position and every ERC-20 token the address held at or before the pinned height, including contract address, symbol, decimals, and token standard.

Four fields drive almost every custodial Avalanche UI. `balances.value` per `currency` covers AVAX and every ERC-20 in the wallet. `currency.address` is the contract address of the token, which is the join key against an internal whitelist of supported assets. `currency.decimals` is the divisor for human-readable display. `blocks.timestamp.time` is the snapshot age, shown next to the balance in the UI so the user knows when the number was captured.

## Why Anchoring at a Block Height Matters

A balance query without a pinned height returns the latest indexed value. That number changes between two calls made one second apart, which is fine for a live view and useless for a report. A balance query with `height: {lteq: 60000000}` returns a deterministic value. The same address and height pair returns the same number forever.

Determinism unlocks two things. Reproducible reporting comes first. A statement generated tomorrow for last Friday's holdings has to match the statement generated today for last Friday's holdings. Pinning the height guarantees it. Cache reuse comes second, covered below.

To pick the right block for a given target wall-clock time, the second query in the pattern is the [Avalanche block-before-timestamp query in the Bitquery GraphQL IDE](https://ide.bitquery.io/avalanche-block-before-timestamp?utm_source=blog&utm_campaign=avalanche_balances).

```
{
  ethereum(network: avalanche) {
    blocks(
      date: {till: "2026-05-01T00:00:00Z"}
      options: {desc: "height", limit: 1}
    ) {
      height
      timestamp {
        time(format: "%Y-%m-%dT%H:%M:%SZ")
      }
    }
  }
}
```

Set `till` to the target timestamp, get the block height of the last block at or before that time. Feed the height into the balances query above. The pair of calls produces a balance snapshot for any historical timestamp the product needs to report against.

## Batched Variant for Fan-Out

A single-address query works for the per-user wallet view. A custodian polling thousands of addresses on a refresh cycle wants the batched variant. The `address` filter accepts a list.

```
{
  ethereum(network: avalanche) {
    address(address: {in: [
      "0x794a61358D6845594F94dc1DB02A252b5b4814aD",
      "0xb4315e873dBcf96Ffd0acd8EA43f689D8c20fB30",
      "0x9ab2De34A33fB459b538c43f251eB825645e8595"
    ]}) {
      address {
        address
      }
      balances(height: {lteq: 60000000}) {
        value
        currency {
          address
          symbol
          decimals
          tokenType
        }
      }
    }
  }
}
```

Most custodians settle on batches of 50 to 100. Smaller batches deliver fresher results per address with more wall-clock parallelism. Larger batches reduce request count at the cost of higher per-response latency. The right number depends on how many addresses the product manages and how concurrent the polling layer is.

## How Caching Turns Repeated Polls into Zero-Cost Reads

Custodial workloads are dominated by repetition. The same customer address is polled every refresh cycle, every page load, every reconciliation pass. [Bitquery](https://bitquery.io/?utm_source=blog&utm_campaign=avalanche_balances) caches the response per `(address, height)` cache key and serves the cached snapshot back to the next caller. Cached responses do not consume API points.

Once a block is final on the C-Chain, the answer to `balances(height: {lteq: ...})` at that finalized height for any address is fixed forever. Every subsequent caller asking the same question gets the same cached bytes. The first call pays the points. Replays hit cache.

Reconciliation runs against pinned historical heights resolve almost entirely from cache. The same end-of-day snapshot polled by audit, customer support, and the user dashboard converges on the same cache key.

End-of-month and end-of-quarter reporting runs against the same set of heights every time the report is regenerated. Re-runs hit cache.

This is the single biggest economic difference between Bitquery and running raw RPC against your own archive node. Raw RPC has no shared cache layer across callers. Every call walks the node. Bitquery amortises the same address read across every caller asking the same question.

## Scaling Profile

The pattern above describes a typical custodial or treasury workload. The cadence is one snapshot per address per day for most products, with optional higher-frequency live polling for the wallet detail page.

The cache hit ratio holds as the address count grows. Every address is its own cache key. The per-address economics for a product managing tens of thousands of addresses are the same as for one managing a few thousand.

The polling cadence is the main lever for cost and freshness. Polling every 15 minutes instead of daily multiplies request count by 96. Most of that increase resolves from cache because Avalanche balances change only when a transfer touches the address. The freshness gain is real for active trading wallets, mostly cosmetic for cold storage and idle treasuries.

The ERC-20 array is variable cost. Returning the full `balances` list per address includes every token the wallet has ever held. Addresses holding hundreds of low-cap tokens cost more to serialize than AVAX-only wallets. If the product only displays a curated asset list, filter `currency.address` client side, or filter on a known asset address set in the query.

## Bitquery vs Self-Hosted Avalanche Archive Node and Indexer

The natural alternative is running an Avalanche archive node alongside an EVM indexer such as a custom Erigon configuration or a hosted-style stack like The Graph. This is a legitimate choice for some teams. It is the wrong choice for most custodians and treasury products. Here is the comparison on the dimensions that matter at custodial scale.

| Dimension | Self-hosted archive + indexer | Bitquery GraphQL |
|---|---|---|
| Time to first balance query | Multi-day initial sync from genesis | Minutes (sign up, key, query) |
| Disk requirements | 2 TB and growing for archive state | None |
| RAM requirements | 32 to 64 GB recommended | None |
| Schema migrations | Required on every Avalanche upgrade | Handled by Bitquery |
| ERC-20 token decoding | Build your own log parser or run a token registry | Built in via `currency` block |
| Historical balance at height | Archive node required, slow query path | Native `balances(height: {lteq: ...})` |
| High availability | Customer builds and operates | Built in |
| Cost of daily polls across thousands of addresses | Hardware plus ops plus on-call | Zero GraphQL points on cache hits |
| Multi-chain coverage | Separate node fleet per chain | Same API, different network argument |

The case for an archive node is sub-50-millisecond latency on private queries, full control over the execution-layer state schema, and the ability to run analytical queries Bitquery does not expose. The case against an archive node is everything else.

Operationally, running an Avalanche archive node fleet means paying the cost of every consensus client upgrade in lost engineering time. State pruning is delicate. Disk grows continuously. Monitoring, backup, and failover are the team's problem. None of this is unique to Avalanche. The cost has to be paid in addition to whatever else the team is building.

## Wiring This Into a Custodial Avalanche Backend

A typical custodial integration uses four components.

A timestamp-to-height resolver runs the block-before-timestamp query once per snapshot interval (every day for end-of-day, every hour for hourly) and caches the height mapping. The resolver is the cheapest component in the pipeline because the result is a single integer per timestamp.

A batched balance fetcher fires the batched `address: {in: [...]}` variant against the resolved height for every batch of customer addresses. Output is parsed into per-address records covering AVAX and the supported ERC-20 set. Records are written to the custodian's own database and surfaced in the customer UI.

A reconciliation job runs the same query against the same height on a slower cadence (weekly) and compares the polled snapshot against the custodian's internal ledger. Drift indicates either a missed transfer event or a misattributed deposit. Both are addressable in operations rather than waiting for a customer complaint.

A historical statement generator reuses the same two queries to produce statements for any past period. Because the queries pin to a height, statements are reproducible across re-runs. An audit or amended statement six months later returns the same numbers without paying for fresh API reads.

Avalanche C-Chain balances change every time a transfer touches an address, which can be many times per minute for actively traded wallets. Polling every five minutes makes sense for active trading products. Polling every five minutes for a cold-storage treasury does not. Most custodians settle on per-minute or per-15-minute for active wallets and hourly or daily for everything else.

## Summary

A custodial Avalanche product polling thousands of customer addresses for AVAX and ERC-20 balances has three real choices. Build an Avalanche archive node and indexer stack and operate it. Pay an RPC provider per call. Or use Bitquery's cached GraphQL layer with the block-height anchoring pattern and pay zero GraphQL points on the repeat traffic that dominates the workload.

The third option exists specifically because the repetition pattern is so universal across custodians, treasury tools, and portfolio products. The two queries in this article are the canonical query shapes these products converge on.

## Related Resources

- [Polling Cardano Stake and Reward Balances at Scale: A Guide for Custodians](/blog/polling-cardano-stake-and-reward-balances-at-scale)
- [How to Detect Cardano Deposits Without Running a Full Node](/blog/how-to-detect-cardano-deposits-without-running-a-full-node)
- [Tracking Per-User ADA Staking Rewards for Tax and Accounting](/blog/tracking-per-user-ada-staking-rewards-for-tax-and-accounting)
- [Avalanche Address API schema](https://docs.bitquery.io/v1/docs/Schema/Avalanche/address?utm_source=blog&utm_campaign=avalanche_balances)
- [Avalanche Blocks API schema](https://docs.bitquery.io/v1/docs/Schema/Avalanche/blocks?utm_source=blog&utm_campaign=avalanche_balances)
- [Avalanche schema overview](https://docs.bitquery.io/v1/docs/Schema/Avalanche/overview?utm_source=blog&utm_campaign=avalanche_balances)
- [Avalanche blockchain API overview](https://bitquery.io/blockchains/avalanche-api?utm_source=blog&utm_campaign=avalanche_balances)
- [Getting started with the Bitquery GraphQL IDE](https://docs.bitquery.io/v1/docs/graphql-ide/how-to-start?utm_source=blog&utm_campaign=avalanche_balances)
