---
title: "157 Billion Solana Rows Per Week: How Bitquery Handles the Pump.fun Volume Problem Across Four Delivery Channels"
url: "https://bitquery.io/blog/solana-pumpfun-volume-four-delivery-channels"
date: "2026-06-02"
author: "Divyasshree"
feed_url: "https://bitquery.io/blog"
---
Bitquery processed roughly 95 million GraphQL queries with sub-second response times while streaming roughly 157 billion rows across customer subscriptions in a single week driven by Solana's Pump.fun ecosystem. The platform deploys four coordinated data delivery channels - GraphQL queries, WebSocket subscriptions, Kafka streams, and cloud data dumps - each optimized for different use cases from interactive lookups to warehouse analytics. All channels share centralized node infrastructure with server-side filter pushdown to manage the extreme throughput demands of Solana's transaction volume.
