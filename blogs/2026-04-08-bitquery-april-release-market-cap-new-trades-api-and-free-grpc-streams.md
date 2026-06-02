---
title: 'Bitquery April Release: Market Cap, New Trades API & Free gRPC Streams'
url: https://bitquery.io/blog/bitquery-april-2026-release
date: '2026-04-08'
author: ''
feed_url: https://bitquery.io/feed
---
Hello Bitquerian,

Here is what is new this month:

- **Market cap and supply** in crypto price APIs and Kafka streams, with an improved weighted algorithm for USD pricing.
- **Solana gRPC (Corecast)** streaming available at no cost for eligible use.
- **Unified multi-chain Crypto Trades API** with price, USD, supply, and trader context so you can track P&L and other trader metrics in one place.

## New market cap and supply data

We have added market cap and supply fields to our crypto price APIs so you can go deeper on token context.

- Real-time **market cap** and **supply** metrics (circulating, total, FDV) across tokens, pairs, and currencies via query or streaming.

Explore the [Crypto Price API documentation](https://docs.bitquery.io/docs/trading/crypto-price-api/) to try it in your stack.

## New price algorithm for the Crypto Price API

The updated index aims for more accurate USD pricing and a more reliable real-time price stream.

- Uses a **1-hour rolling window** with **exponentially weighted volume** so recent trades weigh more heavily and prices stay responsive yet stable.
- **Filters low-quality trades** and **aggregates across pools and pairs** for volume-weighted token prices.

Read the full methodology: [Price index algorithm](https://docs.bitquery.io/docs/trading/crypto-price-api/price-index-algorithm/).

## Free gRPC Corecast streaming for Solana

gRPC streaming for Solana is now available **free** (per current program terms on our site and docs).

- Low-latency, filtered real-time Solana streams over custom gRPC with structured, decoded chain data.
- **Topic-style subscriptions** (trades, transfers, pools, and more) with server-side filtering to keep streams efficient.

Get started: [Solana gRPC introduction](https://docs.bitquery.io/docs/grpc/solana/introduction/).

Product overview: [Solana gRPC streams](https://bitquery.io/products/solana-grpc-streams).

## New Crypto Trades API

Stream real-time DEX trades across chains, including **Solana, Ethereum, BSC, Base, Arbitrum, and Polygon**.

Each trade can include:

- Price and **USD** notionals
- **Market cap** and **FDV**
- **Supply** metrics
- **Trader** wallet
- **Transaction** identifiers and related metadata

Documentation: [Crypto Trades API](https://docs.bitquery.io/docs/trading/crypto-trades-api/trades-api/).

Event streams expose swap-level trade data with full token context, including supply and market cap snapshots where applicable.

**Kafka:** schema for topic `trading.trades` is in [`market/trades.proto`](https://github.com/bitquery/streaming_protobuf/blob/main/market/trades.proto). For a Kafka trial, contact [sales@bitquery.io](mailto:sales@bitquery.io).

## DEXRabbit updates

### New screens and insights

DEXRabbit now includes additional screens for Solana traders and researchers:

[Solana Signals Feed](https://dexrabbit.bitquery.io/solana/signals-feed) — curated signal-oriented view of on-chain activity.

![](/blog/bitquery-april-2026-release/signals.png)

[Pumpfun Trenches](https://dexrabbit.bitquery.io/solana/pumpfun?insight=trenches) 
— explore Pumpfun activity with the Trenches insight layout

![](/blog/bitquery-april-2026-release/trenches.png)

We will keep shipping more actionable insights; follow [DEXRabbit](https://dexrabbit.bitquery.io/) for updates.

## Interesting videos

For walkthroughs and API deep dives, see the [Bitquery YouTube channel](https://www.youtube.com/@bitquery/videos).

## Get in touch

Questions about Telegram or the community? Say hello on [Telegram](https://t.me/bloxy_info) or the [Bitquery community forum](https://community.bitquery.io/), or email [support@bitquery.io](mailto:support@bitquery.io).

## Explore Bitquery

- [Products and tools](https://bitquery.io/products)
- [Documentation and SDKs](https://docs.bitquery.io/docs/)
- [DEX trades and streaming products](https://bitquery.io/products/dex) · [Trade API](https://bitquery.io/products/trade-api) · [Data streams](https://bitquery.io/products/data-streams)
