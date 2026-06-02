---
title: How to Detect Cardano Deposits Without Running a Full Node
url: https://bitquery.io/blog/how-to-detect-cardano-deposits-without-running-a-full-node
date: '2026-05-22'
author: ''
feed_url: https://bitquery.io/feed
---
A custodial [Cardano](https://bitquery.io/blockchains/cardano-blockchain-api?utm_source=blog&utm_campaign=cardano_deposits) product has two real time data problems. The first is showing customers their balances, native asset holdings, and staking rewards, which is covered in the companion [guide on polling Cardano stake and reward balances at scale](/blog/polling-cardano-stake-and-reward-balances-at-scale). The second is detecting incoming deposits and outgoing withdrawals against thousands of customer addresses without operating `cardano-node` and `cardano-db-sync`.

This article walks through the deposit detection architecture used by production custodians on Bitquery. It covers the [Cardano inputs and outputs query](https://docs.bitquery.io/v1/docs/Examples/cardano/inputs-outputs?utm_source=blog&utm_campaign=cardano_deposits) shape, the tip of chain heartbeat that drives the walker, the block range chunk sizing tradeoff, the address book reconciliation step, and the failure modes worth designing around.

## The Three Component Architecture

Every production custodial deposit detector reduces to three components.

The first component is a tip of chain heartbeat. It fires on a fixed schedule and returns the current Cardano block height. This tells the rest of the pipeline how far the chain has advanced since the last sweep.

The second component is a block range walker. It calls the [Cardano `inputs` and `outputs` queries](https://docs.bitquery.io/v1/docs/Schema/cardano/inputsOutputs?utm_source=blog&utm_campaign=cardano_deposits) with `height: {gteq: X, lteq: Y}` filters and a list of customer addresses, then pages through the results. Each iteration extends the walked range by a fixed chunk size until the cursor catches up to tip.

The third component is a reconciler. It joins the walker's output against the custodian's internal address book, attributes each transaction to a customer account, and writes a deposit or withdrawal event to the ledger.

The three components run independently. The heartbeat decides when there is new work. The walker does the work. The reconciler turns the work into business state. Failures in one do not cascade into the others.

## Tip of Chain Heartbeat

The heartbeat is one query that returns the current block height. Open the [Cardano blocks query in the Bitquery GraphQL IDE](https://ide.bitquery.io/cardano-tip-of-chain-block-height?utm_source=blog&utm_campaign=cardano_deposits) to test it.

```
{
  cardano(network: cardano) {
    blocks(options: {limit: 1, desc: "height"}) {
      height
      timestamp {
        time(format: "%Y-%m-%d %H:%M:%S")
      }
    }
  }
}
```

This is the cheapest query in the pipeline. Cache the response per second on your own side if the heartbeat fires more often than that. Most custodians run the heartbeat every 30 to 60 seconds, which matches the Cardano block production interval.

The heartbeat does two jobs. It tells the walker the upper bound of the next chunk to fetch. It also signals chain liveness, so a heartbeat that fails repeatedly is the earliest indicator that something upstream is wrong.

## Block Range UTXO Walker

The walker is the workhorse query. It fetches every `input` and every `output` touching a list of customer addresses inside a bounded block range. Open the [Cardano deposit walker block range query in the Bitquery GraphQL IDE](https://ide.bitquery.io/cardano-deposit-walker-block-range?utm_source=blog&utm_campaign=cardano_deposits) to run it.

```
{
  cardano(network: cardano) {
    inputs(
      inputAddress: {in: [
        "addr1v9m34968vfwya2dydafkaq48ag9pzerznwjf0ewu4jj5vfsvgmyhk",
        "addr1qxz3ve4caaywwg6q82ax9l5xknyc7juvwwsw20cpugyz5gv9zent3m6guu35qw46vtlgddxf3a9ccuaqu5lsrcsg9gss69fhxw"
      ]}
      height: {gteq: 10000000, lteq: 10005000}
      options: {asc: ["block.height", "inputIndex"], limit: 100, offset: 0}
    ) {
      block {
        height
        timestamp {
          time(format: "%Y-%m-%d %H:%M:%S")
        }
      }
      transaction {
        hash
      }
      inputIndex
      inputAddress {
        address
      }
      value
      currency {
        symbol
        tokenId
      }
    }
    outputs(
      outputAddress: {in: [
        "addr1v9m34968vfwya2dydafkaq48ag9pzerznwjf0ewu4jj5vfsvgmyhk",
        "addr1qxz3ve4caaywwg6q82ax9l5xknyc7juvwwsw20cpugyz5gv9zent3m6guu35qw46vtlgddxf3a9ccuaqu5lsrcsg9gss69fhxw"
      ]}
      height: {gteq: 10000000, lteq: 10005000}
      options: {asc: ["block.height", "outputIndex"], limit: 100, offset: 0}
    ) {
      block {
        height
        timestamp {
          time(format: "%Y-%m-%d %H:%M:%S")
        }
      }
      transaction {
        hash
      }
      outputIndex
      outputAddress {
        address
      }
      outputDirection
      value
      currency {
        symbol
        tokenId
      }
    }
  }
}
```

In production this query would take `$addresses`, `$startHeight`, `$endHeight`, `$limit`, and `$offset` as variables driven by the walker's cursor and the heartbeat. The block range in the example above is illustrative.

The `outputs` array is the deposit feed. Each entry is value flowing into a customer address. The `inputs` array is the withdrawal feed. Each entry is value flowing out of a customer address. The pairing of the two arrays in one query halves the round trip count.

Note three field choices that matter at custodial scale. The `address: {in: [...]}` filter takes an array, so a single call covers a batch of customer addresses, not one at a time. The `currency { symbol, tokenId }` selection covers ADA and every Cardano native token in the same response, so the walker is asset agnostic. The `asc` ordering on `block.height` plus `inputIndex` or `outputIndex` gives a deterministic, paginatable cursor.

The chunk size (the height range per call) is a tunable, not a fixed number. A smaller chunk keeps each query short and safe against timeouts, at the cost of more round trips and more bookkeeping per chunk. A larger chunk means fewer round trips, but each query touches more rows and risks running into the upstream timeout during high chain activity. The right size depends on the address count, the activity level on those addresses, and the upstream timeout. Run the walker at production address count for a few hours, measure the p95 query duration, and tune from there.

## Catching Up From Genesis vs Staying At Tip

The walker has two operating modes.

Steady state runs at tip. The walker watches the heartbeat, and when a new block lands it fetches the chunk from `lastWalkedHeight + 1` to `tipHeight`. Most of the time this chunk is small (one to a few hundred blocks) and resolves in well under a second.

Catch up mode runs when the walker is more than one chunk behind tip. This happens after deployment, after a service restart that lost the cursor, or after extended downtime. The walker fetches chunk after chunk in sequence until the cursor reaches tip, at which point it falls back to steady state.

The catch up rate on Bitquery is bounded by query throughput, not chain throughput. A custodian backfilling six months of history can typically catch up in hours rather than days. Compare this to syncing `cardano-db-sync` from genesis, which is a multi day operation against current chain size.

## Address Book Reconciliation

The walker returns chain level data. Each `output` carries the address that received value, the transaction hash, and the amount, but the chain has no concept of which customer that address belongs to. That mapping lives inside the custodian's own systems. The reconciler is the component that joins the two: it takes each output the walker found and looks the recipient address up against the custodian's internal address book to attribute the deposit to a customer.

The reconciler holds two tables. The first is the address book, mapping every customer to the deposit addresses generated for them. The second is the ledger, the custodian's source of truth for deposits and withdrawals.

For every `output` returned by the walker, the reconciler looks up the recipient address in the address book, attributes the value to a customer, and writes a deposit event to the ledger keyed on transaction hash and output index. For every `input`, it does the same, this time as a withdrawal event.

The transaction hash plus output index pair is the natural idempotency key. The walker is allowed to re run any chunk safely because the reconciler will deduplicate any output it has already seen.

A practical scaling note. The address book lookup is the hot path. Keep it in memory or in a low latency key value store, not a query against the main relational database. A custodian with 50,000 addresses needs sub millisecond lookups per output, not 10 millisecond round trips.

## Bitquery vs Self Hosted cardano-db-sync for Deposit Detection

The natural alternative is running `cardano-db-sync` against `cardano-node` and querying Postgres directly. This is a legitimate choice for some teams. It is the wrong choice for most custodians. Here is the comparison on the dimensions that actually matter for a deposit detector at custodial scale.

| Dimension | Self hosted cardano-db-sync | Bitquery GraphQL |
|---|---|---|
| Time to first deposit detected | Multi day initial node sync | Minutes (sign up, key, query) |
| Operating components | cardano-node + cardano-db-sync + Postgres | One HTTP endpoint |
| Disk requirements | Approximately 600 GB and growing | None |
| Catch up from history | Re sync from genesis (days) | Walk block ranges (hours) |
| Schema migrations | Required on every Cardano protocol upgrade | Handled by Bitquery |
| High availability | Customer builds and operates | Built in |
| Multi chain coverage | Separate node fleet per chain | Same API, different network argument |
| Address batch filter | Build it in Postgres | Built in via `address: {in: [...]}` |

The case for db-sync is sub 50 millisecond latency on private queries, full control over the Postgres schema, and very large historical scans (multi year, full chain). The case against db-sync is everything else.

Operationally, running a node fleet for deposit detection means paying the cost of every Cardano protocol upgrade in lost time. Schema migrations on `db-sync` are non trivial. Node downtime stops the deposit feed cold. None of these failure modes exist on Bitquery's hosted layer.

## Reliability and Operational Concerns

Three failure modes matter for a deposit detector.

The walker stalls and the cursor falls behind tip. The custodian's view is correct but stale, and deposits show up to customers late. The mitigation is monitoring on `tipHeight - lastWalkedHeight`. Anything above one chunk worth of blocks for more than a few minutes should page the on call.

The walker returns a deposit, but the address is not in the address book. This means the custodian generated an address and forgot to register it with the reconciler, or the reconciler's address book is out of sync. Treat this as a hard alert. Unattributed deposits are a customer support incident.

A reorg invalidates a recently walked block. Cardano has finality guarantees that make this rare, but it can happen near the tip. The mitigation is to delay deposit confirmation by a small confirmation buffer (15 to 30 blocks) so any deposit credited to a customer is past the realistic reorg window. The walker can still fetch every block at tip, but the reconciler holds events in a pending state until they clear the buffer.

## Putting it Together

The full deposit detector loop is short.

```
every 30 to 60 seconds:
    tipHeight = heartbeat()

    if tipHeight - lastWalkedHeight > chunkSize:
        offset = 0
        while chunk not exhausted:
            results = walker(nextChunk, limit, offset)
            for each input, output in results:
                reconciler.apply(result, key=(transaction.hash, inputIndex or outputIndex))
            offset += limit

        # only advance the cursor once the reconciler has persisted the chunk
        if reconciler.confirmed(chunk):
            lastWalkedHeight = chunkEndHeight
```

The custodian's deposit and withdrawal feeds are now driven by Cardano chain state with no node fleet to operate.

This is the same architecture used by production Cardano custodians on Bitquery. It pairs with the [polling guide on Cardano stake and reward balances at scale](/blog/polling-cardano-stake-and-reward-balances-at-scale) to give a complete data layer for a custodial product: balances on one path, deposits and withdrawals on the other.

## Summary

A custodial Cardano product detecting deposits and withdrawals at scale has three real choices. Build and operate a Cardano node fleet and `cardano-db-sync`. Stream from a managed RPC provider and reconstruct UTXO movements per call. Or run a tip heartbeat plus a block range walker against [Bitquery](https://bitquery.io/?utm_source=blog&utm_campaign=cardano_deposits) and let the reconciler handle attribution.

The third option exists specifically because the deposit detection pattern is so universal across exchanges and custodial wallets. The queries in this article are the canonical query shapes these products converge on.

## Related Resources

- [Polling Cardano Stake and Reward Balances at Scale: A Guide for Custodians](/blog/polling-cardano-stake-and-reward-balances-at-scale)
- [Cardano Inputs and Outputs API examples](https://docs.bitquery.io/v1/docs/Examples/cardano/inputs-outputs?utm_source=blog&utm_campaign=cardano_deposits)
- [Cardano Inputs and Outputs schema](https://docs.bitquery.io/v1/docs/Schema/cardano/inputsOutputs?utm_source=blog&utm_campaign=cardano_deposits)
- [Cardano Blocks API examples](https://docs.bitquery.io/v1/docs/Examples/cardano/blocks?utm_source=blog&utm_campaign=cardano_deposits)
- [Cardano blockchain API overview](https://bitquery.io/blockchains/cardano-blockchain-api?utm_source=blog&utm_campaign=cardano_deposits)
- [Cardano schema overview](https://docs.bitquery.io/v1/docs/Schema/cardano/overview?utm_source=blog&utm_campaign=cardano_deposits)
- [Getting started with the Bitquery GraphQL IDE](https://docs.bitquery.io/v1/docs/graphql-ide/how-to-start?utm_source=blog&utm_campaign=cardano_deposits)
