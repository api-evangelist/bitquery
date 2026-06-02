---
title: 'Polling Cardano Stake and Reward Balances at Scale: A Guide for Custodians'
url: https://bitquery.io/blog/polling-cardano-stake-and-reward-balances-at-scale
date: '2026-05-22'
author: ''
feed_url: https://bitquery.io/feed
---
A custodial [Cardano](https://bitquery.io/blockchains/cardano-blockchain-api?utm_source=blog&utm_campaign=cardano_staking) product has the same data problem every day. It needs to display the current ADA balance, native asset holdings, staked amount, and claimable rewards for every customer address it manages. It needs to refresh that data on a clock so the UI is never stale. It needs to do this for thousands of addresses without operating a node fleet and without paying per call for repeated polls.

This article walks through the [Cardano staking query](https://docs.bitquery.io/v1/docs/Examples/cardano/address?utm_source=blog&utm_campaign=cardano_staking) pattern, the caching behaviour that makes repeat polls free, the scaling profile observed in production, and the buy versus build comparison against self hosted `cardano-db-sync`.

## Cardano Address Query for Balance, Native Tokens, and Staking

The query a custodian needs is a single `cardano.address` lookup that returns balances, native tokens, and the full staking snapshot in one round trip. Open the [Cardano balances and staking snapshot query in the Bitquery GraphQL IDE](https://ide.bitquery.io/cardano-balances-native-tokens-staking-snapshot?utm_source=blog&utm_campaign=cardano_staking) to run and modify it against any address.

```
{
  cardano(network: cardano) {
    address(
      address: {is: "addr1q8wp4tc65hgvvff0lg2dgp8dnd2hy5u4vyz9mgk8qancwmtdf8tun55syv9gvfd0dgdhx02vlyg6dp56up92a5l9qxhs9nhrfy"}
    ) {
      address {
        address
        annotation
      }
      balance {
        value
        currency {
          name
          symbol
          decimals
          address
          tokenId
          tokenType
        }
      }
      staking {
        controlledTotalStake
        stakedAmount
        stakedAmountWithRewards
        rewardsAmount
        rewardsAvailable
        withdrawnAmount
        address {
          address
          annotation
        }
      }
    }
  }
}
```

Five fields drive almost every custodial staking UI. `balance.value` per `currency` covers ADA and every native token held in the address. `staking.stakedAmount` is the principal currently delegated. `staking.rewardsAvailable` is the amount the user can withdraw right now. `staking.rewardsAmount` is the lifetime reward total. `staking.controlledTotalStake` is the full stake controlled by the address, useful for pool operator dashboards.

The query works against both base addresses (`addr1q...` for value transfer with a stake credential attached) and stake addresses (`stake1...` for delegation lookups). Pass either format to the same `is:` filter.

## How Caching Turns Repeated Polls into Zero Cost Reads

Custodial workloads are dominated by repetition. The same customer address is polled every refresh cycle, every page load, every reward distribution check. [Bitquery](https://bitquery.io/?utm_source=blog&utm_campaign=cardano_staking) caches the response per address and serves the cached snapshot back to the next caller. Cached responses do not consume API points.

A representative custodial workload looks like this. Thousands of unique customer addresses are polled on a roughly daily cadence. The cache hit ratio on the repeat traffic approaches 100% because every address is polled with the same query shape and the address state changes only at known intervals. The GraphQL point cost on this slice of traffic is effectively zero.

Over a two month window this means the bulk of polling traffic resolves against cached snapshots. The custodian only pays for the data fetches that actually have to recompute (block range walks for deposit detection, covered in a separate guide).

This is the single biggest economic difference between Bitquery and running raw RPC against your own node. Raw RPC has no shared cache layer across callers. Every call walks the node and burns compute. Bitquery amortises the same address read across every caller asking the same question.

## Scaling Profile

The shape above describes a typical mid sized custodian. The cadence is one refresh per address per day on average. The product is a customer facing wallet view, not a real time trading dashboard, so a daily refresh is sufficient.

Three observations carry through to larger and smaller deployments.

The cache hit ratio holds as the address count grows. Adding more customer addresses to the polling pool does not degrade hit rate because every address is its own cache key. The per address economics for a custodian managing tens of thousands of addresses are the same as for one managing a few thousand.

The polling cadence is the main lever for cost and freshness. A custodian polling every 15 minutes instead of daily multiplies request count by 96. The cache layer absorbs almost all of that increase because Cardano staking state changes once per epoch (every five days), not every minute. The freshness gain is real for balance display but mostly cosmetic for stakes and rewards.

The native asset field is variable cost. Returning the full `balance` array per address includes every native token in the wallet. Addresses holding hundreds of project tokens cost more to serialize than ADA only addresses. If your product only displays ADA balance, filter `currency.symbol` client side and consider an explicit `currency: {is: "ADA"}` filter to cut payload size.

## Bitquery vs Self Hosted cardano-db-sync

The natural alternative is running `cardano-db-sync` against a Cardano node. This is a legitimate choice for some teams. It is the wrong choice for most custodians. Here is the comparison on the dimensions that actually matter at custodial scale.

| Dimension | Self hosted cardano-db-sync | Bitquery GraphQL |
|---|---|---|
| Time to first query | Multi day initial sync from genesis | Minutes (sign up, key, query) |
| Disk requirements | Approximately 600 GB and growing | None |
| RAM requirements | 24 to 32 GB recommended | None |
| Schema migrations | Required on every Cardano protocol upgrade | Handled by Bitquery |
| High availability | Customer builds and operates | Built in |
| Cost of daily polls across thousands of addresses | Hardware plus ops plus on call | 0 GraphQL points on cache hits |
| Multi chain coverage | Separate node fleet per chain | Same API, different network argument |
| Caching | Build it yourself | Built in |

The case for db-sync is sub 50 millisecond latency on private queries, full control over the Postgres schema, and the ability to run queries that Bitquery does not expose (rare). The case against db-sync is everything else.

Operationally, the engineering effort to run a reliable Cardano node fleet is non trivial. Every protocol upgrade requires schema migration and resync coordination. Disk grows continuously. Monitoring, backup, and failover are the team's problem. None of this is unique to Cardano, but the cost has to be paid in addition to whatever else the team is building.

## Wiring This Into a Custodial Backend

A typical custodial integration uses three components.

A job scheduler fires the `address` query for each customer address on a fixed cadence (daily for most products). The output is parsed into a per address record covering ADA, native tokens, staked amount, and claimable rewards. The record is written to the custodian's own database and surfaced in the customer facing UI.

A withdrawal trigger watches `staking.rewardsAvailable` per address and surfaces a UI affordance when the value crosses a threshold. This makes the reward withdrawal flow self serve without requiring a separate query.

A reconciliation job runs against the same query on a slower cadence (weekly) and compares the polled snapshot against the custodian's internal ledger. Drift indicates either a missed deposit or a misattributed stake delegation. Both are addressable in operations rather than waiting for a customer complaint.

Worth noting: stake and reward state on Cardano updates once per epoch (every five days). Polling balance every five minutes makes sense for active trading products. Polling rewards every five minutes does not. Most custodians settle on hourly or daily for rewards and either real time (via the deposit walker, covered separately) or hourly for balances.

## Summary

A custodial Cardano product polling thousands of customer addresses for balance, stake, and rewards has three real choices. Build a Cardano node fleet and operate it. Pay an RPC provider per call. Or use Bitquery's cached GraphQL layer and pay zero GraphQL points on the repeat traffic that dominates the workload.

The third option exists specifically because the repetition pattern is so universal across custodians, exchanges, and staking as a service products. The query in this article is the canonical query shape these products converge on.

## Related Resources

- [Cardano Address API examples](https://docs.bitquery.io/v1/docs/Examples/cardano/address?utm_source=blog&utm_campaign=cardano_staking)
- [Cardano Address schema](https://docs.bitquery.io/v1/docs/Schema/cardano/address?utm_source=blog&utm_campaign=cardano_staking)
- [Cardano AddressStats schema](https://docs.bitquery.io/v1/docs/Schema/cardano/addressstats?utm_source=blog&utm_campaign=cardano_staking)
- [Cardano Inputs and Outputs API examples](https://docs.bitquery.io/v1/docs/Examples/cardano/inputs-outputs?utm_source=blog&utm_campaign=cardano_staking)
- [Cardano blockchain API overview](https://bitquery.io/blockchains/cardano-blockchain-api?utm_source=blog&utm_campaign=cardano_staking)
- [Cardano schema overview](https://docs.bitquery.io/v1/docs/Schema/cardano/overview?utm_source=blog&utm_campaign=cardano_staking)
- [Getting started with the Bitquery GraphQL IDE](https://docs.bitquery.io/v1/docs/graphql-ide/how-to-start?utm_source=blog&utm_campaign=cardano_staking)
