---
title: 'Tracking NFT Wash Trading on Solana: Techniques and Case Studies'
url: https://bitquery.io/blog/how-to-track-nft-wash-trading
date: '2024-12-11'
author: ''
feed_url: https://bitquery.io/feed
---
[Crypto wash trading](https://bitquery.io/blog/wash-trading-solana-crypto-market-manipulation-detection) is a deceptive practice that involves simultaneously buying and selling the same asset to create an illusion of increased trading activity and price changes. This article explains the mechanics of wash trading, delves into NFT-specific wash trading, and presents detailed case studies to identify and analyze potential instances of manipulation.

![](/blog/how-to-track-nft-wash-trading/trades.gif)

### What is Wash Trading?

Wash trading is performed by a trader or a group of traders through repetitive buy-and-sell cycles of the same asset. This creates misleading impressions of:

- High liquidity: Suggesting the asset is easy to buy or sell.
- Market demand: Artificially inflating the perception of interest.
- Price manipulation: Causing price surges or dips that mislead other investors.

### How Wash Trading is Performed

Below are the primary tactics used in wash trading:

1.  Coordinated Buy/Sell Cycles

    Traders execute repeated buy-and-sell transactions for the same asset, often in quick succession. These transactions, matched within the group, do not reflect genuine market behavior but significantly inflate trading volume.

2.  Inflating Perceived Demand

    These transactions artificially enhance trading metrics, such as the number of traders and transaction volume. This gives the false impression of growing investor interest.

3.  Manipulating Prices

    Timing trades strategically, traders can create artificial price fluctuations. These manipulations often result in sudden spikes or dips, further misleading market participants.

![](/blog/how-to-track-nft-wash-trading/flow.png)

### Understanding NFT Wash Trading

NFTs are particularly susceptible to wash trading because:

- Most NFT platforms allow anonymous transactions with no identity checks.
- NFTs can be sold to a wallet controlled by the same owner to inflate their value.

In the case of NFT wash trading, the goal would be to make one’s NFT appear more valuable than it really is by “selling it” to a new wallet the original owner also controls.

### Key Indicators of NFT Wash Trading

While it is close to impossible to identify all collections that carry out wash trading as it involves filtering a large data set based on qualitative factors, these are some common signs to look out for:

- Collection trades at a persistent price level (i.e. no outlier buys for ‘rares’).
- Collection has high trading volume / is trending but has low visibility poor social media metrics (e.g. low Twitter follower count)
- An NFT was bought more than a normal amount of times in a day (at Footprint Analytics we estimate this to be more than 3+)
- An NFT was bought repeatedly by the same buyer address over a short period, say 120 minutes

Let's examine four case studies that demonstrate potential wash trading (of tokens) tactics:

### Case Study 1 - PEAGLE:

- Total trading volume: 1412
- Unique traders: 12
- Price change: 754.2%

The extremely low number of unique traders (12) compared to the total trading volume of 1,412 is a clear red flag. This suggests a small group of traders are engaging in repetitive buy/sell cycles to inflate the trading activity. The dramatic 754.2% price increase further points to artificial price manipulation.

[View Data Here](https://ide.bitquery.io/Peagle-trades_1)

[https://explorer.bitquery.io/solana/address/Es5xC3r6PnixrdGZgdZeDXJ5piDDrjWrapRr7GZ4pump](https://explorer.bitquery.io/solana/address/Es5xC3r6PnixrdGZgdZeDXJ5piDDrjWrapRr7GZ4pump)

![](/blog/how-to-track-nft-wash-trading/peagle.png)

### Case Study 2 - THOG:

- Total trading volume: 192
- Unique traders: 6
- Price change: 484.7%

Again, the relatively low number of unique traders (6) transacting such a high volume of 192 indicates potential wash trading. A small group could be responsible for most of the buy/sell activity to drive up the price, which surged 484.7%.

[View Data Here](https://ide.bitquery.io/THOG-trades)

[https://explorer.bitquery.io/solana/address/D9ouW29KQEG8jeHnEpuaeqJ4D4QYfyDMRaajCf6rhfCW](https://explorer.bitquery.io/solana/address/D9ouW29KQEG8jeHnEpuaeqJ4D4QYfyDMRaajCf6rhfCW)

![](/blog/how-to-track-nft-wash-trading/thog.png)

### Case Study 3 - GIGATRUMP:

- Total trading volume: 80
- Unique traders: 6
- Price change: 477.5%

With only 6 unique traders accounting for 80 total trades, this is an obvious case of potential wash trading. The price increase of 477.5% is another strong indicator of artificial price inflation.

[View Data Here](https://ide.bitquery.io/GIGATRUMP-trades)

[https://explorer.bitquery.io/solana/address/Bx9kzi4Xs2V5ms392rc24Mc6GwoGoXERPRzQpeLMpump](https://explorer.bitquery.io/solana/address/Bx9kzi4Xs2V5ms392rc24Mc6GwoGoXERPRzQpeLMpump)

![](/blog/how-to-track-nft-wash-trading/gigatrump.png)

### Case Study 4 - cPANZR:

- Total trading volume: 33
- Unique traders: 4
- Price change: 434.7%

While the trading volume of 33 is substantial, the low number of unique traders (4) suggests a small group may be responsible for cycling buy/sell orders to boost trading activity and prices, which climbed 434.7%.

[View Data Here](https://ide.bitquery.io/cPANZR-trades_2)

[https://explorer.bitquery.io/solana/address/F1L5ECK8zRDuUVBN1ZoGrT9EHjj83L4udRhARDwnpump](https://explorer.bitquery.io/solana/address/F1L5ECK8zRDuUVBN1ZoGrT9EHjj83L4udRhARDwnpump)

![](/blog/how-to-track-nft-wash-trading/panzr.png)

### Conclusion

The recurring patterns across these case studies reveal the hallmarks of wash trading:

- Disproportionately low unique trader counts compared to total trading volume.
- Significant price surges within short timeframes.

This deceptive activity distorts the integrity of cryptocurrency markets. Ongoing monitoring and regulation are crucial to detect and mitigate such manipulative practices.

---

_This material is for educational and informational purposes only and is not intended as investment advice. The content reflects the author's personal research and understanding. While specific investments and strategies are mentioned, no endorsement or association with these entities is implied. Readers should conduct their own research and consult with qualified professionals before making any investment decisions. Bitquery is not liable for any losses or damages resulting from the application of this information._

---

_Written by Abhishek_
