---
title: 'Build Solana Bubble Maps with Bitquery: Historical Data with Zero RPC Calls'
url: https://bitquery.io/blog/solana-bubblemaps-bitquery-zero-rpc
date: '2025-10-31'
author: ''
feed_url: https://bitquery.io/feed
---
Traditional Solana historical queries usually chain multiple RPCs: call `getSignaturesForAddress`, loop each signature through `getTransaction`, and sometimes walk `getBlock` for context. For active traders, this quickly explodes into hundreds or thousands of requests—slow, costly, and complicated.

Running your own RPC also isn’t a silver bullet. Nodes periodically download snapshots; if slots are missed during catch‑up, the “blocks behind” metric oscillates as the node alternates between downloading, verifying, and following new slots.

**“Zero RPC calls for archival solana data, is that possible?”**

For use cases like bubble maps, you don’t want every on-chain detail; you want the exact subset of transfers, shaped for aggregation and visualization. Bitquery makes this possible by letting you query precisely the data you need in one request.

Deep pagination across billions of records is slow, you need stable ordering and resumable crawls. Even simple tasks like finding the first transfer of an address or the creator of a token, can require paging backward indefinitely.

What’s more, accurate USD amounts need historical price at event time, per token, with decimals handled correctly.

Of course, you can build your own indexer. But then you own storage, infra, maintenance, and the problems that come with it.

Bitquery solves this problem for [historical solana data](https://docs.bitquery.io/v1/docs/Examples/Solana/transfers). You describe the data you want (e.g., transfers for a receiver address since a date, filtered by token), and the API returns exactly those rows, ready for aggregation into a bubble map.

## Why Bitquery is the Best Solution for Historical Solana Data

- Pre-indexed and parsed data: Bitquery maintains a indexed, parsed representation of all Solana transactions. Unlike raw RPC responses that require parsing instruction data, extracting transfer details, and handling various transaction formats, Bitquery provides clean, structured transfer data ready for querying.
- Targeted data: Ask exactly for the transfers you need in one query rather than stitching `getSignaturesForAddress` + `getTransaction` + `getBlock`.
- Predictable performance: Server-side filtering, ordering, and pagination reduce client loops and retries.
- Flexible Querying: GraphQL lets you select only fields needed for analytics/visualization (amounts, addresses, timestamps).

In short, for building things like bubble maps, you care about a subset of transfer edges over a time window with totals—Bitquery gives this directly.

Now let’s fetch historical Solana transfers for a bubblemap—end‑to‑end with Bitquery.

We’ll query only the transfers we need (filtered by token, receiver, and time), return USD amounts, and shape the result for bubblemap visualization.
We will focus on one question:

_“Which wallets have interacted with this address historically?”_

## Simple Tutorial: Build a Solana Bubble Map from Bitquery

This project includes a script using d3.js library that fetches transfers via Bitquery and generates a ready-to-open bubblemap visualization.

![](/blog/solana-bubblemaps-bitquery-zero-rpc/bubblemap.gif)

### Repository

[Complete code is available here](https://github.com/Divyn/solana-bubblemap-generator/tree/main).

### Prerequisites

- Node.js 18+
- A Bitquery account and an OAuth token
- Clone this repository to your system

### Install dependencies

```
npm install axios fs open
```

### Configure your Bitquery token

Once you have an OAuth token, add it an environment variable and import it in the code.

### How the script works

- Builds a GraphQL query targeting solana transfers for a given `receiverAddress` and optional currency token address. You can run the query on Bitquery's IDE [here](https://ide.bitquery.io/map-query-working_1)
- Paginates results and aggregates into:

- nodes: unique addresses with their USD volume
- links: edges between sender → receiver with summed USD value and count

- Embeds data directly into index.html, writes output.html, and opens it in your default browser.

### Run it

Using it as an NPM Script:

```
npm run build:bubblemap --      [since_date]
```

- currency_address: SPL token mint address to filter transfers by (e.g. USDT or a project token). Use null to fetch native SOL transfers.
- receiver_address: The focal address whose inbound transfers form the bubble map neighborhood.
- since_date: ISO date (YYYY-MM-DD). Defaults to 2025-09-24 if omitted.

Or call Node directly:

```
node scripts/build-bubblemap.js     [since_date]
```

Example (USDT → a project wallet since 2025‑09‑24):

```
npm run build:bubblemap -- Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB CapuXNQoDviLvU1PxFiizLgPNQCxrsag1uMeyk6zLVps 2025-09-24
```

What you get:

- The script writes output.html at the repo root and opens it.
- output.html contains an embedded dataset and D3 force-directed chart:

- Bubble size: node USD volume
- Link width: summed USD transferred along that edge
- Tooltip: address, node USD, and edge stats

### Adjusting Parameters

You can adjust any of the existing parameters to query a larger dataset:

- pageSize (default 100): Increase to reduce round trips; decrease if you hit timeouts.
- maxPages (default 4): Raises/lowers the total data fetched.
- interPageSleepMs and retry/backoff settings: Helpful when rate-limited.
- nodes.slice(0, 300) and links.slice(0, 1000): Caps visualization complexity for readability.

output.html is a standalone page—no server required. Share it as-is or publish it via GitHub Pages/Netlify. The source index.html is a template during the build.

To preview locally with a static server:

```
npm run serve
```

### Common issues

- “Missing BITQUERY_OAuth_token” or 401: Ensure your OAuth token is valid and present in the script.
- Timeouts or rate limits: Reduce pageSize, increase timeoutMs, and let retries/backoff work.
- Empty map: Double-check currency_address, receiver_address, and since_date are correct and that there is actual activity.

## Beyond Historical Data: Realtime Streaming

While Bitquery's GraphQL API excels at historical data queries, you can also access real-time Solana blockchain data through [Bitquery's gRPC Streams](https://docs.bitquery.io/docs/grpc/solana/introduction). These streams deliver parsed, filterable events with sub-second latency, making them ideal for trading applications, bots, and DeFi platforms that need instant transaction notifications. For comprehensive documentation on real-time streaming technologies including WebSocket, Kafka, and CoreCast, visit the [Streams documentation](https://docs.bitquery.io/docs/streams/).
