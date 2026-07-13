---
title: "How to Detect Bitcoin Deposits Without Running a Full Node"
url: "https://bitquery.io/blog/how-to-detect-bitcoin-deposits-without-running-a-full-node"
date: "2026-05-28"
feed_url: "https://bitquery.io/feed"
---
A custodial [Bitcoin](https://bitquery.io/blockchains/bitcoin-blockchain-api?utm_source=blog&utm_campaign=bitcoin_deposits) product has the same data problem every other UTXO custodian has. The product needs to detect inbound deposits and outbound withdrawals against thousands of customer addresses, attribute every event to the right customer account, and stay in sync with the chain without operating `bitcoind` and an indexer fleet. This article walks through the deposit reconciliation architecture used by production Bitcoin custodians on Bitquery.
