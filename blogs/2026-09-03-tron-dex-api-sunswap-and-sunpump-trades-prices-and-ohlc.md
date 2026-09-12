---
title: "Tron DEX API: SunSwap and SunPump Trades, Prices and OHLC with GraphQL"
url: "https://bitquery.io/blog/tron-dex-api"
date: "2026-09-03"
feed_url: "https://bitquery.io/feed"
---
Tron's DEX activity runs through a small set of venues: SunSwap, whose v1 pools are the former JustSwap and whose v2 and v3 pools follow the Uniswap v2 and v3 designs, SunPump, the bonding-curve launchpad whose graduates move to SunSwap, and a StableSwap pool set for stablecoins. Bitquery indexes all of them, and this post shows the queries for trades, prices, candles, traders, new launches and wallet history on the [Tron blockchain](https://explorer.bitquery.io/tron). The 2021 version of this post announced the first Tron DEX support on the V1 API; every query below runs on the current API an
