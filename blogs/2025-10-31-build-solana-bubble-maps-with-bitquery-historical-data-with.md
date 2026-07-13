---
title: "Build Solana Bubble Maps with Bitquery: Historical Data with Zero RPC Calls"
url: "https://bitquery.io/blog/solana-bubblemaps-bitquery-zero-rpc"
date: "2025-10-31"
feed_url: "https://bitquery.io/feed"
---
Traditional Solana historical queries usually chain multiple RPCs: call `getSignaturesForAddress`, loop each signature through `getTransaction`, and sometimes walk `getBlock` for context. For active traders, this quickly explodes into hundreds or thousands of requests—slow, costly, and complicated. Running your own RPC also isn’t a silver bullet.
