---
title: "DEX Data APIs: Get Uniswap Trades, Prices and OHLC in One Query"
url: "https://bitquery.io/blog/dex-data-apis"
date: "2026-09-03"
feed_url: "https://bitquery.io/feed"
---
This post first appeared in 2020, when Uniswap had just overtaken Coinbase in daily volume and the way to get its trades was a set of REST endpoints on Bloxy. Those endpoints have been retired. What replaced them is one GraphQL schema that returns Uniswap v2, v3 and v4 swaps with USD prices attached, works unchanged on every chain Uniswap runs on, and streams the same rows over WebSocket or Kafka.
