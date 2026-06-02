---
title: Solana's Volume Numbers Are a Lie. Three Wallets Are Inflating Them.
url: https://bitquery.io/blog/solana-volume-numbers-are-a-lie
date: '2026-04-28'
author: ''
feed_url: https://bitquery.io/feed
---
We pulled twelve hours of Solana DEX trade data on April 27 and 28, 2026. We expected to find bot activity on memecoins. We did not expect to find it traceable to three named wallets.

One of those wallets did the whole job in fifty two seconds. It seeded two hundred bots with exactly half a SOL each. The bots then wash traded a single memecoin to fake more than half a million dollars of volume in twelve hours. The next two wallets ran similar operations on different tokens with one extra layer of laundering to throw off naive tracing tools. We name all three further down, with Solscan links and the disbursement signatures.

This is what wash trading on Solana actually looks like.

## The hard numbers on OpenLie

[OpenLie](https://solscan.io/token/FHMpPNaPxQJcyCJBYF7LddH9up2wDPnoGUdi1H6CFcZ6) (`FHMpPNaPxQJcyCJBYF7LddH9up2wDPnoGUdi1H6CFcZ6`) reported $532,461 in DEX volume over the 12 hour window. That volume came from 233 wallets running 40,523 trades. The average wallet made 174 trades. The average trade was $13.14. 96 percent of the wallets bought AND sold the same token. The buy USD and sell USD totals per wallet were nearly identical. The round trip ratio for our top 10 wash wallets ranged from 0.95 to 1.00.

Real markets do not look like this. Real traders do not appear on both sides of the same token 96 percent of the time. Real traders do not buy and sell the same dollar amount across 200 trades. Real volume does not arrive in tight $13 increments at four orders of magnitude over the natural rate. This is bot driven roundtripping. Full stop.

## Five tokens, three identifiable funders

We sampled the top five wash candidates ranked by trades per trader and average trade size. Three of the five trace back to a single funder wallet. The other two are diffusely funded with bot generated wallets and we could not isolate a single root in the 12 hour window.

| Token | Funder wallet | Funding pattern |
|---|---|---|
| [OpenLie](https://solscan.io/token/FHMpPNaPxQJcyCJBYF7LddH9up2wDPnoGUdi1H6CFcZ6) | [`Etvs47JBdMA5qkGWbiGzgz9XQRtkTmhGVh6MjaV5V39F`](https://solscan.io/account/Etvs47JBdMA5qkGWbiGzgz9XQRtkTmhGVh6MjaV5V39F) | Direct seed of at least 200 bots with exactly 0.5 SOL each, in a 52 second burst on 2026-04-21 00:59:53 to 01:00:45 UTC |
| [JFDS](https://solscan.io/token/GKiqDc8apRFsBkYBKGCT5Wsbacz9ixoNYSnkshoWpump) | [`EZn8SDd5HvNaNgg3jGVLeou2QTvSwgBWUSpJjxAjw2T`](https://solscan.io/account/EZn8SDd5HvNaNgg3jGVLeou2QTvSwgBWUSpJjxAjw2T) | Received 569 SOL on 2026-04-26, then one hop laundered through 27 ephemeral intermediates |
| [S&P500](https://solscan.io/token/pY4dq8Fz3hSeRNdWZ3nJM623TreF1S1isi19UWrpump) | [`6iTTuZemuS4kWQzUkZfrxwcgz1va6FeZcGDW6sW8ZiQn`](https://solscan.io/account/6iTTuZemuS4kWQzUkZfrxwcgz1va6FeZcGDW6sW8ZiQn) | Aged wallet (since January 2026) using 0.08 SOL micro funding per intermediate |
| [babyai16z](https://solscan.io/token/38fXPsdJz2jo2KE1GM5b6N8f7d4ARDDnLyB8EH2npump) | diffuse | Each bot funded by a unique one time sender with 0.002 SOL of dust |
| [QOTUS](https://solscan.io/token/HBDViVBPEqRYrCsF4qbFmF98M7KwmuPup7eFz5vXpump) | diffuse | Each bot funded by a unique one time sender with 0.002 SOL of dust |

The S&P500 case is interesting because it is not volume inflation. The wash trades run at 18 cents of buy and 16 cents of sell per wallet across 400 trades. That is too small to fake a volume ranking and too systematic to be retail. That is price pinning.

The diffuse cases (babyai16z and QOTUS) almost certainly use a "volume bot" service that auto generates funding wallets. These services are advertised on Telegram. They are cheap. The orchestrator is real, just one or two extra hops further out than our 12 hour SOL only trace could see.

You can verify the OpenLie disbursement burst yourself in the [Bitquery MCP](https://mcp.bitquery.io/) in under a minute.

## How we found it in two phases

The investigation runs in two phases. Phase one is wash detection. Phase two is origin tracing.

We used the [Bitquery Crypto Price MCP](https://mcp.bitquery.io/) for phase one. The MCP indexes every DEX trade across eight chains down to one second resolution. We ranked Solana tokens by trade count over the 12 hour window. We computed trades per trader, average trade size in USD, and the share of wallets on both sides of the market. A composite wash score isolated the top suspects in seconds.

We used the [Bitquery Solana transfers API](https://docs.bitquery.io/v1/docs/Examples/Solana/transfers) for phase two. We pulled the first inbound SOL transfer for every wash wallet. We aggregated by sender. The dominant sender for [OpenLie](https://solscan.io/token/FHMpPNaPxQJcyCJBYF7LddH9up2wDPnoGUdi1H6CFcZ6) matched 30 out of 30 wallets we had flagged independently. The match was not coincidence. The match was confirmation.

The full methodology, the queries, and the raw data are all in the [public repository](https://github.com/divyasshree-BQ/solana-wash-trading). Anyone with a Bitquery API key can reproduce this. Anyone willing to spend 30 minutes can verify the OpenLie case end to end.

## Why this is a problem worth caring about

Solana sells itself on volume. Volume rankings drive listings. Volume rankings drive attention. Volume rankings drive capital allocation. Wash trading at this scale corrupts every one of those signals.

Token launchpads boast about daily volume that is mostly bots. Aggregator dashboards rank tokens that are mostly bots. Influencers shill tokens that are mostly bots. Retail buyers see "high volume" and assume liquidity. They are buying into a price that has been pinned by a 0.5 SOL army.

This is not a Solana-specific failure mode. It is a problem with most on-chain trading.

## What you can do about it

Stop trusting raw volume numbers. Demand wash adjusted volume from your data sources. Check the trader count, not just the trade count. Check the round trip ratio for the top wallets, not just the volume number. If 90 percent of the activity is from wallets that bought and sold the same dollar amount, you do not have liquidity. You have theatre.

The [Bitquery MCP](https://mcp.bitquery.io/) makes this kind of audit cheap. The [Bitquery Solana transfers API](https://docs.bitquery.io/v1/docs/Examples/Solana/transfers) makes the funder tracing trivial. Anyone with the API key and 30 minutes can reproduce the [OpenLie](https://solscan.io/token/FHMpPNaPxQJcyCJBYF7LddH9up2wDPnoGUdi1H6CFcZ6) analysis.

We are publishing the queries. We are publishing the wallet lists. We are publishing the runner script. There is no excuse for any analytics platform to keep promoting wash inflated tokens.

## Resources

**Reproduce this report:** [`github.com/divyasshree-BQ/solana-wash-trading`](https://github.com/divyasshree-BQ/solana-wash-trading)

**Bitquery Crypto Price MCP:** [`mcp.bitquery.io`](https://mcp.bitquery.io/)

**Bitquery GraphQL IDE:** [`ide.bitquery.io`](https://ide.bitquery.io/)

### Tokens flagged

[OpenLie](https://solscan.io/token/FHMpPNaPxQJcyCJBYF7LddH9up2wDPnoGUdi1H6CFcZ6) (`FHMpPNaPxQJcyCJBYF7LddH9up2wDPnoGUdi1H6CFcZ6`)

[JFDS](https://solscan.io/token/GKiqDc8apRFsBkYBKGCT5Wsbacz9ixoNYSnkshoWpump) (`GKiqDc8apRFsBkYBKGCT5Wsbacz9ixoNYSnkshoWpump`)

[S&P500](https://solscan.io/token/pY4dq8Fz3hSeRNdWZ3nJM623TreF1S1isi19UWrpump) (`pY4dq8Fz3hSeRNdWZ3nJM623TreF1S1isi19UWrpump`)

[babyai16z](https://solscan.io/token/38fXPsdJz2jo2KE1GM5b6N8f7d4ARDDnLyB8EH2npump) (`38fXPsdJz2jo2KE1GM5b6N8f7d4ARDDnLyB8EH2npump`)

[QOTUS](https://solscan.io/token/HBDViVBPEqRYrCsF4qbFmF98M7KwmuPup7eFz5vXpump) (`HBDViVBPEqRYrCsF4qbFmF98M7KwmuPup7eFz5vXpump`)

### Funder wallets

OpenLie funder [`Etvs47JBdMA5qkGWbiGzgz9XQRtkTmhGVh6MjaV5V39F`](https://solscan.io/account/Etvs47JBdMA5qkGWbiGzgz9XQRtkTmhGVh6MjaV5V39F)

JFDS funder [`EZn8SDd5HvNaNgg3jGVLeou2QTvSwgBWUSpJjxAjw2T`](https://solscan.io/account/EZn8SDd5HvNaNgg3jGVLeou2QTvSwgBWUSpJjxAjw2T)

S&P500 funder [`6iTTuZemuS4kWQzUkZfrxwcgz1va6FeZcGDW6sW8ZiQn`](https://solscan.io/account/6iTTuZemuS4kWQzUkZfrxwcgz1va6FeZcGDW6sW8ZiQn)

### The full OpenLie bot fleet

The 200 wallet addresses Etvs seeded are published as flat files in the repo:

[`data/openlie_fleet_wallets.txt`](https://github.com/divyasshree-BQ/solana-wash-trading/blob/main/data/openlie_fleet_wallets.txt) — one address per line, 200 lines.

[`data/openlie_fleet_wallets.csv`](https://github.com/divyasshree-BQ/solana-wash-trading/blob/main/data/openlie_fleet_wallets.csv) — same addresses with amount, block height, block time, and the funding transaction signature on each row.

### Verification

OpenLie disbursement window: 2026-04-21 00:59:53Z to 2026-04-21 01:00:45Z (the 200 seed transfers all landed in the first three seconds; the two larger residual sends of 22 SOL and 47.92 SOL extended the outbound window to 01:00:45)

Sample disbursement transaction: [`DJ7JWUWEGPR3wGUWkfk6QWz9KDTXY6tvMPwY9ksvUtBJzWGtJyyjHgTPCkc86iUhDC9pWb8UNd3PhGQNqutHu9x`](https://solscan.io/tx/DJ7JWUWEGPR3wGUWkfk6QWz9KDTXY6tvMPwY9ksvUtBJzWGtJyyjHgTPCkc86iUhDC9pWb8UNd3PhGQNqutHu9x)

Verification query (one click, query and variables pre-loaded in the Bitquery IDE): [`ide.bitquery.io/Solana-washtrading-verify-query`](https://ide.bitquery.io/Solana-washtrading-verify-query)

Full list of wallets that received money from `Etvs47JBdMA5qkGWbiGzgz9XQRtkTmhGVh6MjaV5V39F`: [Google Drive link](https://drive.google.com/file/d/1Q4Y-BvYZN0aQIpGxtQYv2Va1efYTRAry/view?usp=sharing)

Expected result: 200 outbound SOL transfers from `Etvs47JBd...V39F`, each exactly 0.5 SOL, all within the 52 second window from 00:59:53 to 01:00:45 UTC. Two larger residual sends (22 SOL and 47.92 SOL) appear right after the burst.

-----

### Legal disclaimer

This article is based entirely on public on-chain data from the Solana blockchain. The wash trading attribution rests on statistical heuristics (round trip ratios, both sides participation, funding burst patterns) and on-chain evidence, not on access to the wallet operators' off-chain identities or intent. The wallet addresses named here are public on-chain identifiers, not identified persons. We are not asserting that the named wallets or their controllers have committed any crime under any specific jurisdiction. Wash trading legality varies by jurisdiction and by the specific facts of each case. Nothing in this article is legal advice, financial advice, or investment advice. Readers can reproduce the analysis independently using the queries and data linked in the Resources section. If you control one of the named wallets and believe the analysis is mistaken, please reach out with the relevant evidence and we will publish a correction.
