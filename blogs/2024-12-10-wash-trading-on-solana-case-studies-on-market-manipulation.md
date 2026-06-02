---
title: 'Wash Trading on Solana: Case Studies on Market Manipulation'
url: https://bitquery.io/blog/wash-trading-solana-crypto-market-manipulation-detection
date: '2024-12-10'
author: ''
feed_url: https://bitquery.io/feed
---
In the financial markets, one theme consistently spans across all sectors: manipulation. Bad actors often attempt to exploit the market for their gain. In the crypto ecosystem, one of the common methods these players use is wash trading.

This article will explore how wash trading works, its impact on the [Solana](https://bitquery.io/blockchains/solana-blockchain-api) ecosystem, tools for detecting wash trading, and real-life examples of wash trading on Solana.

## Wash Trading in the Crypto Ecosystem

Wash trading is a form of market manipulation where prices are artificially inflated to mislead investors to believe market liquidity is higher than it actually is.

In cryptocurrency, wash trading occurs when an individual repeatedly buys and sells the same asset to create the illusion of heightened trading activity. This manipulation artificially inflates the asset's trading volume, often leading to an increase in its price. By boosting the volume, these tokens are intentionally manipulated for profit.

## How Wash Trading Works

As mentioned earlier, wash trading involves the repeated buying and selling of an asset by the same person or group to create an illusion of increased trading activity, which leads to artificial price movement.

So, what methods do these actors use to engage in wash trading? Let’s explore.

- Artificial Volume: When traders enter numerous buy and sell orders of the same token within a short timeframe, the trading volume of that token becomes artificially inflated, giving the token a false impression of its importance, and thereby attracting investors.

- Trading Bots: Traders also exploit bots to execute trades automatically, making the process efficient and harder to detect. These bots enter numerous buy and sell orders of the same token within a short timeframe to sustain the illusion of market activity.

- Exchanges: Some traders may target smaller exchanges because they are less regulated than bigger exchanges, and they lack the tools to track wash trading.

Some of the impacts of Wash Trading include;

- Disruption of Market Data: Where false trading volumes and price action disrupt the natural flow of the market structure. This can lead to inaccurate market trends and data.

- Deceiving Investors: The effects of wash trading can be detrimental to investors who rely on trading volume to make informed decisions. It creates a false sense of an asset's popularity, often leading to investments in overvalued or poorly performing assets, resulting in financial losses.

- Price Bubble: The artificial surge in trading activity creates an unstable price bubble that can collapse during a price correction, resulting in losses for investors.

By understanding the dynamics of how wash trading works, investors and regulators can take steps to reduce it by promoting transparency in cryptocurrency markets.

### Why Wash Trading Occurs on Solana

[Solana](https://bitquery.io/blog/inside-solana-instructions-types-exploration) is popular because it has low transaction fees. This attracts wash traders because the frequent transactions they intend to make would be cheap when compared to blockchains with higher fees. By paying low fees, these actors can increase the trading volume of an asset artificially to mislead investors into believing a token is highly active and in demand.

Another reason why Solana is susceptible to wash trading is its speed. It is a a high-speed blockchain that has the ability to process thousands of transactions per second,thanks to its low latency and high throughput features, Solana supports high-frequency trading which allows rapid execution of buy and sell orders. This makes it easy to inflate trading volumes and create the illusion of liquidity.

### Indicators of Wash Trading

The following are patterns that may be an indication of wash trading:

- Back and forth trades: Repeated trades between two or more wallets over a short period of time might indicate wash trading.
- Sudden Spikes: Sharp increase in trading volume or price without a corresponding market news or trend could suggest wash trading acivity.
- Circular Transactions: When a token is bought and sold within seconds or minutes by the same wallet address, it could indicate wash trading activity. This transaction simulates high-trading while retaining balance. In most of these trades, the wallet may have no profit or loss, so its just to manipulate market volume.

## Detecting Wash Trading with On-Chain Data

Here, we will explore examples of projects or tokens on Solana that are suspected of or have been identified in wash trading activities by bad actors.

### Case Study 1 : GGSS

[GGSS](https://dexrabbit.com/solana/pair/v62Jv9pwMTREWV9f6TetZfMafV254vo99p7HSF25BPr/EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v#pair_latest_trades)

![](/blog/wash-trading-solana-crypto-market-manipulation-detection/ggss.jpg)

The wallet address[AvVqenaRxjsczBdY3AwtXosVoxF8dnNaBn5bptSx3isF](https://dexrabbit.com/solana/trader/AvVqenaRxjsczBdY3AwtXosVoxF8dnNaBn5bptSx3isF) exhibited consistent and repetitive buying and selling activities.The repetitive transaction pattern from this wallet aligns with the characteristics of wash trading, indicating an intent to distort market metrics and misrepresent genuine trading interests.

![](/blog/wash-trading-solana-crypto-market-manipulation-detection/ggss_trades.jpg)

#### Inflow and Outflow

The inflow of the wallet from 2024-06-1 to 2024-11-30 has only been between GgsS and USDC as seen in the query:[https://ide.bitquery.io/Inflow-for-wallet-address](https://ide.bitquery.io/Inflow-for-wallet-address)

The Outflow of the wallet from 2024-06-1 to 2024-11-30 has been between GgsS and USDC as seen in the query: [https://ide.bitquery.io/Outfloww-for-wallet](https://ide.bitquery.io/Outfloww-for-wallet)

![](/blog/wash-trading-solana-crypto-market-manipulation-detection/ggss_explorer.png)

#### Wallet Past History

From the [query](https://ide.bitquery.io/transactions-for-AvVqenaRxjsczBdY3AwtXosVoxF8dnNaBn5bptSx3isF), it shows that the wallet engaged in multiple trades involving GgsS.

The token was traded over **40,000 times**, which was unusual given that the wallet was inactive prior to this activity.

### Case Study 2 : AKINYA

[AKINYA](https://dexrabbit.com/solana/pumpfun/GG2gsJUccSiqorFYYZ2v9qcA9bm16FgJ52b9aHsUpump#top_trades)

![](/blog/wash-trading-solana-crypto-market-manipulation-detection/akinya.png)

A particular wallet address [2VhzzuwHMFCxS4mS2ksJCPrsrpDM8etsrdbrSXUhdRtF](https://dexrabbit.com/solana/trader/2VhzzuwHMFCxS4mS2ksJCPrsrpDM8etsrdbrSXUhdRtF)

engaged in constant buying and selling in order to artificially increase volume.

![](/blog/wash-trading-solana-crypto-market-manipulation-detection/akinya_trades.jpg)

#### Wallet Past History:

- From past history as seen in the [query](https://ide.bitquery.io/transactions-for-2VhzzuwHMFCxS4mS2ksJCPrsrpDM8etsrdbrSXUhdRtF_1) from 2024-06-1 to 2024-11-30, the wallet was active before these trades.
- The wallet strategically selects a particular token to trade, engages in repetitive buying and selling of the same token, which artificially increases the trading volume.This can be seen in the query response where some tokens are traded more than 30k times.
- This pattern does not align with a typical market behavior ( diversified trading/ holding/ staking of tokens). Instead, it shows deliberate attempts to manipulate the perception if demand for the token.

### Case Study 3 : Got'em

Got’em (You Looked)

![](/blog/wash-trading-solana-crypto-market-manipulation-detection/gotem.jpg)

A particular wallet address [8aAmHmRiFoAydUGtEkGiYMwR5t3LTY9HEWJNSZ7KMEso](https://dexrabbit.com/solana/trader/8aAmHmRiFoAydUGtEkGiYMwR5t3LTY9HEWJNSZ7KMEso)

engaged in constant buying and selling in order to artificially increase volume.

![](/blog/wash-trading-solana-crypto-market-manipulation-detection/gotem_trades.jpg)

#### Wallet Past History:

From past history as seen in the [query](https://ide.bitquery.io/transactions-for-8aAmHmRiFoAydUGtEkGiYMwR5t3LTY9HEWJNSZ7KMEso) from 2024-06-1 to 2024-11-30, the wallet was active before these trades.

#### Observations

- The wallet focuses on a particular token, trades it multiple times, before choosing a new one. This can be seen in the query response where some tokens are traded more than 30k times.
- An example (Got’em/WSOL) is shown above where the wallet shows repeated transactions of buying and selling the same token pair within a short period of time. This behavior shows an attempt to increase trading volume.
- This pattern does not align with a typical market behavior ( diversified trading/ holding/ staking of tokens). Instead, it shows deliberate attempts to manipulate the perception of demand for the token.

## Conclusion

Wash trading presents a major challenge for Solana blockchain and the crypto market in general. It undermines trust and misleads investors by distorting the natural

structure of the market. By understanding this practice and through the use of tools like Bitquery, we can detect irregular patterns, combat manipulation, and promote market integrity.

---

_This material is for educational and informational purposes only and is not intended as investment advice. The content reflects the author's personal research and understanding. While specific investments and strategies are mentioned, no endorsement or association with these entities is implied. Readers should conduct their own research and consult with qualified professionals before making any investment decisions. Bitquery is not liable for any losses or damages resulting from the application of this information._

---

Written by Edgar N
