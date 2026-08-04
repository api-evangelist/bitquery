---
title: "Streaming Solana Data Without a Node - Agave logsSubscribe, programSubscribe and accountSubscribe vs Bitquery Kafka Streams"
url: "https://bitquery.io/blog/solana-rpc-websockets-vs-bitquery-kafka-streams"
date: "2026-07-27"
feed_url: "https://bitquery.io/feed"
---
Say you want to know when someone buys a token on Pump.fun. On a Solana node, the closest thing to that is `logsSubscribe`, which hands you a signature and a list of log strings. Turning those strings into "wallet X bought 1.2M of mint Y for 3.4 SOL" takes a second RPC call, a Borsh decoder you wrote, and a metadata lookup for the symbol.
