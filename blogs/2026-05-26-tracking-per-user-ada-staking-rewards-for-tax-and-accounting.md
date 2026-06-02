---
title: Tracking Per-User ADA Staking Rewards for Tax and Accounting
url: https://bitquery.io/blog/tracking-per-user-ada-staking-rewards-for-tax-and-accounting
date: '2026-05-26'
author: ''
feed_url: https://bitquery.io/feed
---
Every ADA staking reward is a tax event twice. The protocol credits the reward to the stake address at an epoch boundary. The user pulls the reward into a payment address in a separate on-chain withdrawal transaction. Different jurisdictions treat one or both as the taxable moment. Whichever applies, the CSV your tax engine imports needs both timestamps and the ADA price at each one.

This article is the tax and accounting companion to the existing custodial Cardano series. The companion guide on [polling Cardano stake and reward balances at scale](/blog/polling-cardano-stake-and-reward-balances-at-scale) covers the underlying [Cardano staking query](https://docs.bitquery.io/v1/docs/Examples/cardano/address?utm_source=blog&utm_campaign=cardano_tax) and the cache economics that make repeat reads free. The companion guide on [detecting Cardano deposits without running a full node](/blog/how-to-detect-cardano-deposits-without-running-a-full-node) covers the block range walker that observes withdrawal transactions on chain. This piece is for the engineer building the per-user reward statement that feeds [crypto tax platforms](https://bitquery.io/?utm_source=blog&utm_campaign=cardano_tax) (Koinly, CoinTracker, Cryptio, ZenLedger) or the custodial back-office producing year-end statements.

The same five columns answer both audiences. This article walks through the two queries that produce them, the epoch math, the fiat conversion step, the CSV shape, and the operational concerns specific to tax reporting.

## The Two Tax Events Per Reward

There are two reward events on Cardano. Most tax frameworks care about at least one. Many care about both.

The first event is **earned**. At each epoch boundary the Shelley reward calculation credits the stake address with rewards from two epochs back. The reward arrives without any user action. Some jurisdictions treat this credit as ordinary income. The ADA price at the credit moment becomes the cost basis for the new ADA.

The second event is **withdrawn**. The user signs an on-chain transaction that moves accumulated rewards out of the reward account into a payment address. This is a distinct event with its own transaction hash and timestamp. Other jurisdictions treat the withdrawal as the taxable moment. The ADA price at the withdrawal becomes the realised value.

A defensible per-user reward statement records both. The earned stream gives the income recognition timeline. The withdrawn stream gives the cash-out timeline. Tax platforms that accept either model want them as separate rows.

## Cardano Staking Fields in the Bitquery API

The Cardano `address` API returns a `staking` block for any Shelley address or stake address (`stake1...`). Open the [Cardano staking snapshot query in the Bitquery GraphQL IDE](https://ide.bitquery.io/Cardano-stake-data-for-a-stake-address?utm_source=blog&utm_campaign=cardano_tax) to run it.

```
{
  cardano(network: cardano) {
    address(
      address: {is: "stake1u9l0967s948z2v3fzwcrff2rudm9ujhvn4zj9j3auxc74tcnsdf7k"}
    ) {
      address {
        address
        annotation
      }
      staking {
        controlledTotalStake
        stakedAmount
        stakedAmountWithRewards
        rewardsAmount
        rewardsAvailable
        withdrawnAmount
      }
    }
  }
}
```

Five fields drive the tax export. `rewardsAmount` is the lifetime total ADA credited as rewards to this stake key. `rewardsAvailable` is the portion credited but not yet withdrawn. `withdrawnAmount` is the lifetime total moved out of the reward account. `stakedAmount` is the principal currently delegated, used to validate active delegation periods. `stakedAmountWithRewards` is the combined position.

`rewardsAmount` and `withdrawnAmount` are both monotonic. Take a snapshot today, take another at the next epoch boundary, the difference is the activity in that window. This is the property that makes the snapshot approach below work.

## Pattern 1: Per Epoch Reward Snapshot Using Cached Reads

The Cardano protocol credits rewards at every epoch boundary. Mainnet epochs are five days. The reward for epoch N is calculated at the end of epoch N+1 and credited at the start of epoch N+2. A snapshot taken right after each epoch boundary captures the new credit.

The procedure is straightforward.

Snapshot `rewardsAmount` for every stake key at each epoch boundary. Subtract the previous snapshot from the new one. The delta is the reward credited in that epoch. Tag each delta with the epoch start time, which becomes the income event timestamp on the user statement.

Read-heavy work like this is what the Bitquery cache was built for. Repeated `(stake_address, snapshot_time)` reads are served from cache and do not consume API points. The polling profile observed in production custodians on Bitquery confirms this: thousands of stake keys polled across multiple epochs resolve almost entirely from cache after the first pass. For tax workflows this matters twice. The annual reporting run is cheap. Re-running the same period months later for an audit or amended statement reproduces the exact same snapshots at zero cost.

A bookkeeping loop in pseudocode.

```
EPOCH_SECONDS = 5 * 24 * 3600       # mainnet
SHELLEY_GENESIS = 1596059091         # mainnet epoch 208 start, UTC

def epoch_start(epoch):
    return SHELLEY_GENESIS + (epoch - 208) * EPOCH_SECONDS

for epoch in range(start_epoch, end_epoch + 1):
    for stake_addr in stake_keys:
        snap = bitquery_staking_snapshot(stake_addr, at=epoch_start(epoch))
        store(stake_addr, epoch, snap.rewardsAmount, snap.withdrawnAmount)

for stake_addr in stake_keys:
    rows = ordered_snapshots(stake_addr)
    for prev, curr in zip(rows, rows[1:]):
        earned_this_epoch = curr.rewardsAmount - prev.rewardsAmount
        if earned_this_epoch > 0:
            emit_row(stake_addr, curr.epoch, earned_this_epoch, curr.epoch_start_time)
```

The output is one row per stake key per epoch where a reward was credited. A custodian managing 5,000 users across a full year (73 mainnet epochs) produces at most 365,000 rows. Re-runs are free.

## Pattern 2: On Chain Withdrawal Events

Withdrawals are explicit transactions. The Cardano `transactions` API exposes `withdrawalValue` and `withdrawalCount` at the transaction level. Open the [Cardano withdrawal transactions query in the Bitquery GraphQL IDE](https://ide.bitquery.io/cardano-transactions-with-withdrawals?utm_source=blog&utm_campaign=cardano_tax) to run it.

```
{
  cardano(network: cardano) {
    transactions(
      date: {since: "2025-01-01", till: "2025-12-31T23:59:59"}
      options: {asc: "block.height", limit: 1000}
      withdrawalCount: {gt: 0}
    ) {
      block {
        timestamp {
          time(format: "%Y-%m-%d %H:%M:%S")
        }
        height
      }
      hash
      withdrawalValue
      input_value_usd: inputValue(in: USD)
    }
  }
}
```

To attribute a withdrawal to a specific stake key, join on the stake address used in the transaction's withdrawal certificate. In practice, a custodian already knows which stake key belongs to which user, so the join happens in your application code from the transaction hash and the stake key on file. A crypto tax platform typically receives the stake address from the user during onboarding and runs the same join.

The output is one row per withdrawal: stake key, timestamp, ADA amount, transaction hash. Add the ADA/USD price at the timestamp and you have the cash-out event the tax engine wants.

## Pattern 3: Attaching ADA Price for Fiat Conversion

Two ways to get the price.

The cheapest approach is to use `inputValue(in: USD)` or `outputValue(in: USD)` on the transactions API. Bitquery returns the USD-converted value at the transaction timestamp inside the same query. No second API call, no separate price feed to maintain.

If audit requirements demand a fixed price source (a specific exchange, a daily settlement price, an in-house oracle), keep your own price table indexed by epoch start time. Join on the timestamp from the snapshot or the withdrawal transaction. Auditors generally prefer a documented price source they can verify over an embedded conversion they cannot.

Either approach lands as a single column on each output row.

## CSV Format for Tax Export

Most tax platforms accept a generic CSV in the shape below. The columns map directly to the fields produced by patterns 1 and 2.

```
user_id,stake_address,event_type,event_time_utc,epoch,ada_amount,ada_usd_price,fiat_amount_usd,tx_hash
U-1042,stake1u9l...74tc,earned,2025-01-04 21:44:51,477,12.3814,0.9412,11.6557,
U-1042,stake1u9l...74tc,earned,2025-01-09 21:44:51,478,12.4951,0.9650,12.0578,
U-1042,stake1u9l...74tc,withdrawn,2025-03-22 14:10:02,492,148.7322,0.7401,110.0681,fe3a4d...
```

`event_type` separates the two streams so the tax engine can apply the correct rule per row. `epoch` is empty on withdrawal rows because withdrawals are not epoch-aligned events. `tx_hash` is empty on earned rows because earned events are protocol-level credits at the epoch boundary and have no transaction hash of their own.

Koinly and Cryptio both accept this shape via their generic CSV importer. CoinTracker accepts it after a column rename. The same file opens cleanly in Excel for a CFO review.

## The Epoch N+2 Delay and Statement Timing

Rewards for epoch N are not credited at the end of epoch N. They are credited at the start of epoch N+2, roughly ten days later. The income timestamp on the user statement is the credit time, not the work time. This has two consequences worth documenting in your reporting policy.

For users who unstake mid-epoch, the last reward arrives two epochs after they stop delegating. Most tax platforms expect the credit timestamp as the income date. Stick to that and document the reasoning.

For year-end statements, the rewards from the final December epoch land in early January. Decide your policy in advance. The IRS-style position is that the income event is the credit time, which places the reward in the following tax year. Confirm with the user's accountant. The Bitquery snapshot approach makes either policy straightforward to implement because the snapshot timestamp is explicit on every row.

## Bitquery vs Self Hosted cardano-db-sync for Reward Reporting

The natural alternative is running `cardano-db-sync` against a Cardano node and querying the reward schema directly in Postgres. This is a legitimate choice for some teams. It is the wrong choice for most tax and accounting use cases. Here is the comparison on the dimensions that matter for a reward reporting workflow.

| Dimension | Self hosted cardano-db-sync | Bitquery GraphQL |
|---|---|---|
| Time to first reward statement | Multi day initial sync from genesis | Minutes (sign up, key, query) |
| Disk requirements | Approximately 600 GB and growing | None |
| Reward schema migrations | Required on every Cardano protocol upgrade | Handled by Bitquery |
| Fiat conversion | Bring your own price feed and join | Built in via `(in: USD)` |
| Re run for audit | Re query the node, retest the joins | Cached snapshot, zero points |
| Per epoch backfill across N users | Custom SQL, paginated by hand | One loop, cached after first pass |
| High availability | Customer builds and operates | Built in |

The case for db-sync is sub fifty millisecond latency on private queries and full control over the Postgres schema. The case against db-sync for tax reporting is that the workflow is dominated by re-runs (audit revisions, amended statements, period restatements), and the Bitquery cache turns those re-runs into free reads. A node fleet does not.

## Wiring This Into a Tax or Accounting Backend

A typical reward reporting pipeline has four components.

A snapshot scheduler fires the staking query for every stake key at each epoch boundary and writes the result to a snapshot table indexed by `(stake_address, epoch)`. The cadence is fixed at one snapshot per epoch (every five days) because rewards do not change between epoch boundaries. Higher cadence buys nothing for the rewards columns and only matters for live balance display.

A delta computer reads consecutive snapshots per stake key and emits one earned-event row per non-zero delta. This runs once per epoch immediately after the snapshot scheduler completes.

A withdrawal watcher queries the transactions API for `withdrawalCount: {gt: 0}` over rolling windows, joins each result to the internal stake-key-to-user mapping, and emits one withdrawn-event row per attributed withdrawal.

A CSV builder reads the earned and withdrawn rows for the requested statement period, attaches the ADA/USD price column from either the inline `(in: USD)` value or the in-house price table, and produces the CSV file the tax engine or CFO imports.

The four components are independent. The snapshot scheduler runs on the epoch clock. The withdrawal watcher runs on the chain clock. The delta computer and CSV builder run on the reporting clock. A failure in one does not corrupt the others, and a re-run of any component is cheap because the underlying API reads are cached.

## Summary

A custodial Cardano product or crypto tax platform reporting per-user ADA staking rewards has the same data problem regardless of audience. The CSV needs an event timestamp, an ADA amount, an ADA price at the event, and a stake key attribution, for both the earned moment and the withdrawn moment.

Two GraphQL queries cover both moments. The staking snapshot on the Cardano address API supplies the earned timeline. The Cardano transactions API supplies the withdrawal timeline. The `(in: USD)` aggregator supplies the fiat conversion. The cache turns audit re-runs and amended statements into zero-point reads.

The queries in this article are the canonical query shapes a tax-facing Cardano workflow converges on.

## Related Resources

- [Polling Cardano Stake and Reward Balances at Scale: A Guide for Custodians](/blog/polling-cardano-stake-and-reward-balances-at-scale)
- [How to Detect Cardano Deposits Without Running a Full Node](/blog/how-to-detect-cardano-deposits-without-running-a-full-node)
- [Cardano Address API examples](https://docs.bitquery.io/v1/docs/Examples/cardano/address?utm_source=blog&utm_campaign=cardano_tax)
- [Cardano Address schema](https://docs.bitquery.io/v1/docs/Schema/cardano/address?utm_source=blog&utm_campaign=cardano_tax)
- [Cardano Transactions API schema](https://docs.bitquery.io/v1/docs/Schema/cardano/transactions?utm_source=blog&utm_campaign=cardano_tax)
- [Staking API examples](https://docs.bitquery.io/v1/docs/Examples/Staking/stake_examples?utm_source=blog&utm_campaign=cardano_tax)
- [Cardano blockchain API overview](https://bitquery.io/blockchains/cardano-blockchain-api?utm_source=blog&utm_campaign=cardano_tax)
- [Cardano schema overview](https://docs.bitquery.io/v1/docs/Schema/cardano/overview?utm_source=blog&utm_campaign=cardano_tax)
- [Getting started with the Bitquery GraphQL IDE](https://docs.bitquery.io/v1/docs/graphql-ide/how-to-start?utm_source=blog&utm_campaign=cardano_tax)
