---
title: "How to Detect Stellar Deposits Without Running a Horizon Node"
url: "https://bitquery.io/blog/how-to-detect-stellar-deposits-without-running-a-horizon-node"
date: "2026-05-29"
feed_url: "https://bitquery.io/feed"
---
A custodial [Stellar](https://bitquery.io/?utm_source=blog&utm_campaign=stellar_deposits) product, anchor service, or multi-chain payments backend has the same data problem on Stellar that every other chain custodian has. The product needs to detect inbound deposits and outbound withdrawals against a corpus of user-facing Stellar addresses, attribute every event to the right account, decode native XLM and issued-asset payments alike, and stay in sync with the chain without operating `stellar-core` plus Horizon plus Postgres. This article walks through the data layer Stellar custodians use on B
