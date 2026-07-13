---
title: "Polling Avalanche Wallet Balances at Scale: A Guide for Custodians"
url: "https://bitquery.io/blog/polling-avalanche-wallet-balances-at-scale"
date: "2026-05-27"
feed_url: "https://bitquery.io/feed"
---
A custodial [Avalanche](https://bitquery.io/blockchains/avalanche-api?utm_source=blog&utm_campaign=avalanche_balances) product, a treasury management tool, or a multi-wallet portfolio service all have the same data problem. The product needs the AVAX balance and ERC-20 token holdings for every customer address it tracks, anchored to a specific block height for deterministic reporting, refreshed on a clock so the UI is never stale, across thousands of addresses without operating an Avalanche archive node and without paying per call for repeated polls. This article walks through the [Avalanche a
