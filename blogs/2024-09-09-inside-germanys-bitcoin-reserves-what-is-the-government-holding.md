---
title: 'Inside Germany''s Bitcoin Reserves: What is the Government Holding?'
url: https://bitquery.io/blog/inside-germany-bitcoin-reserves-government-holdings
date: '2024-09-09'
author: ''
feed_url: https://bitquery.io/feed
---
**Quick Take**

- The German Government sold 49,858 BTC for $2.89B - an average price of $57,900 from assets seized from the Movie2K piracy case through a carefully executed sale between June 19, 2024, and July 12, 2024.
- The Bitcoin was sold through multiple exchanges, including Kraken, Coinbase, Cumberland DRW, Bitstamp, and Flowtraders.
- The sale was conducted in collaboration with the Saxon Central Office for the Custody and Utilisation of Virtual Currencies and Bankhaus Scheich Wertpapierspezialist AG.

## The Context: Seized Bitcoin from Movie2K

In a landmark legal enforcement action, German authorities seized nearly 50,000 BTC from the operators of the infamous movie piracy website, Movie2K. The platform, which was notorious for distributing copyrighted films illegally, was shut down by German police ([BKA](https://www.bka.de/EN/Home/home_node.html)) ([bc1qq0l4jgg9rcm3puhhfwaz4c9t8hdee8hfz6738z](https://explorer.bitquery.io/bitcoin/address/bc1qq0l4jgg9rcm3puhhfwaz4c9t8hdee8hfz6738z)), and the digital assets associated with it were confiscated as part of the crackdown.

Under German law, seized assets, including cryptocurrencies, must be liquidated rather than held indefinitely. This approach contrasts with that of other jurisdictions, where seized digital assets might be retained for ongoing investigations or potential future use. Given the volatile nature of Bitcoin according to the German authorities, the decision to proceed with an "emergency sale" under Section 111p of the German Code of Criminal Procedure was made to mitigate the risk of a significant loss of value.

### Historical Bitcoin Seizures

The German government’s Bitcoin liquidation is the latest in a series of high-profile cryptocurrency seizures by law enforcement, marking a significant chapter in the evolving management of digital assets. These events have established important precedents for how such assets are handled and sold, offering valuable lessons for future cases.

One of the earliest and most notable [Bitcoin seizures occurred in 2013](https://bitquery.io/blog/bitcoin-seized-us-government), when the U.S. government shut down Silk Road, a notorious dark web marketplace, seizing approximately 144,000 BTC from its operator, Ross Ulbricht. The U.S. Marshals Service later auctioned off these assets between 2014 and 2015, netting around $48 million—an amount that, in hindsight, underscores the challenges of timing such sales in a volatile market.

Similarly, the 2021 Bitfinex hack saw U.S. authorities recover over 94,000 BTC, valued at roughly $3.6 billion at the time. This seizure highlighted the growing sophistication of law enforcement in tracing and recovering stolen digital assets, with the subsequent sale of these assets being carefully managed to avoid market disruption while retaining a portion for ongoing investigations.

[2021 Bitfinex Hack Source](https://www.chainalysis.com/blog/bitfinex-hack-plea-july-2023/)

In another significant case, the U.S. government in 2022 confiscated 50,000 BTC from James Zhong, who had exploited vulnerabilities in the Silk Road to steal the funds. These assets were strategically liquidated over several months, allowing the government to maximise returns while minimising the impact on the market.

These historical seizures, including the recent German case, provide a blueprint for future digital asset confiscations, emphasising the importance of strategic asset management and market awareness in ensuring successful outcomes. [James Zong 2022 Seizure](https://www.justice.gov/usao-sdny/pr/us-attorney-announces-historic-336-billion-cryptocurrency-seizure-and-conviction)

#### Exchanges Involved in the Liquidation

The German government distributed the Bitcoin across several well-known cryptocurrency exchanges for liquidation:

- Kraken: [3GrFPWMXfD3WZnqxCfbjuxss8wvBfkyoQ8](https://explorer.bitquery.io/bitcoin/address/3GrFPWMXfD3WZnqxCfbjuxss8wvBfkyoQ8)
- Coinbase: [3EHqwauPSQphJa3gYS8g8wfb3WvmazXNf3](https://explorer.bitquery.io/bitcoin/address/3EHqwauPSQphJa3gYS8g8wfb3WvmazXNf3)
- Cumberland DRW: [bc1qmfqnh2urnnefnetxjdw0qjqd7gkaa080myldpn](https://explorer.bitquery.io/bitcoin/address/bc1qmfqnh2urnnefnetxjdw0qjqd7gkaa080myldpn)
- Bitstamp: [3M6DD5tSe7MG7uKr24PyjrfJhtaxVkbZCa](https://explorer.bitquery.io/bitcoin/address/3M6DD5tSe7MG7uKr24PyjrfJhtaxVkbZCa)
- Flowtraders: [1FKAP8U9VABqdMgyN5s521ejEsQo8MA4uw](https://explorer.bitquery.io/bitcoin/address/1FKAP8U9VABqdMgyN5s521ejEsQo8MA4uw)

![](/blog/inside-germany-bitcoin-reserves-government-holdings/pie.png)
These exchanges played a pivotal role in managing and selling the seized BTC, which was strategically distributed across multiple platforms to avoid market disruptions and optimise returns.

![](/blog/inside-germany-bitcoin-reserves-government-holdings/table.png)

---

## Investigative Analysis Using Bitquery

To understand the complexity and strategic nature of these transactions, we leveraged [MoneyFlow](https://bitquery.io/products/moneyflow). These tools were instrumental in retrieving detailed on-chain data that mapped out the intricate web of transactions involved in the German government’s Bitcoin sale. By using Bitquery, we could visualise the flow of funds from the government-controlled wallets to various exchanges, revealing the timing, volume, and potential strategies behind each transfer.

![](/blog/inside-germany-bitcoin-reserves-government-holdings/gov.png)

The Money Flow tool was particularly valuable in this investigation, providing a clear picture of how Bitcoin moved between different wallets and exchanges. For example, the flow to Kraken, Coinbase, and Bitstamp demonstrated a staggered sale approach, avoiding sudden market impacts by strategically spreading out sales over several days. This staggered strategy suggests that the government aimed to prevent significant price drops while maximizing returns.

The data associated with this liquidation provides a unique window into the meticulous planning and execution of the asset sale. Below is a breakdown of the key transactions, wallet movements, and potential strategies employed.

The majority of the Bitcoin, a substantial 13,334 BTC valued at $772 million, was managed by Flow Traders. Kraken and Coinbase each processed significant portions as well, with 6,708 BTC ($393 million) and 5,900 BTC ($347 million) respectively, spread across several days to optimise the sale process.

Bitstamp also handled a sizable amount of 5,800 BTC worth $339 million, while Cumberland DRW managed 1,777 BTC valued at $103 million. The varying dates of these transactions suggest a deliberate approach to mitigate market volatility and maximize the return on these assets.

All these can be easily visualized and analysed using the bitquery explorer easily [here](https://explorer.bitquery.io/bitcoin/address/bc1qq0l4jgg9rcm3puhhfwaz4c9t8hdee8hfz6738z/graph).

![](/blog/inside-germany-bitcoin-reserves-government-holdings/explorer_moneyflow.png)

Source: Money Flow Movement (bc1qq0l4jgg9rcm3puhhfwaz4c9t8hdee8hfz6738z) using Bitquery Explorer [Link](https://explorer.bitquery.io/bitcoin/address/bc1qq0l4jgg9rcm3puhhfwaz4c9t8hdee8hfz6738z/graph)

[BTC Inflow Query](https://ide.bitquery.io/BTC-inflow-German-Police)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeh4LL5N4Bk9aYjulCQqjoT113q_txt3iqJcgFxke7V7D1dVrRm-Nly3Kn4f37aOlr9fo0P1vD2ihIUCE9cI0Z2AFd4wdLY34RmzWW9m7WZrq0Tf86A_aiADOsJ9NmQaeEpaEn5ErGbJV8q2Wm298qY0M0?key=mF25kvFLRUvUWJFYbW1MXQ)The query above can be used to fetch all the inflow transactions for the German govt. BTC address

[BTC Outflow Query](https://ide.bitquery.io/BTC-Outflows-German-Police)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeGx-3TuvUeWW1bBfxemY4gl0WZUzurvaOne8OkcWwi5hOo59OsLnp4kQ5fnTD9FYNvms8a9OjI8Mq0zyMfE_2OHIt-ipyJ3ysRHnaUIZCqBrl3ATK0EyaHCjE5edK92kRMv7JybV-Pzvj7dHUd5vZ-nBsm?key=mF25kvFLRUvUWJFYbW1MXQ)

The query shown above can be used to fetch all the outflow transactions for the german govt. BTC address

### Detailed Transaction Dates and Volumes

#### Kraken Transactions

- Kraken: 3GrFPWMXfD3WZnqxCfbjuxss8wvBfkyoQ8 [Link](https://explorer.bitquery.io/bitcoin/address/3GrFPWMXfD3WZnqxCfbjuxss8wvBfkyoQ8)

![](/blog/inside-germany-bitcoin-reserves-government-holdings/kraken.png)

Source: Money Flow Movement (3GrFPWMXfD3WZnqxCfbjuxss8wvBfkyoQ8) using Bitquery Explorer [Link](https://explorer.bitquery.io/bitcoin/address/3GrFPWMXfD3WZnqxCfbjuxss8wvBfkyoQ8)

- July 12, 2024: 800 BTC ($46.17M)
- July 12, 2024: 400 BTC ($22.90M)
- July 11, 2024: 400 BTC ($22.88M)
- July 11, 2024: 200 BTC ($11.74M)
- July 11, 2024: 375 BTC ($21.77M)
- July 10, 2024: 375 BTC ($22.03M)
- July 10, 2024: 750 BTC ($44.06M)
- July 9, 2024: 400 BTC ($23.02M)
- July 9, 2024: 400 BTC ($22.91M)
- July 8, 2024: 900 BTC ($51.36M)
- July 8, 2024: 200 BTC ($11.46M)
- July 8, 2024: 150 BTC ($8.57M)
- July 4, 2024: 400 BTC ($23.24M)
- July 2, 2024: 32.74 BTC ($2.04M)
- July 1, 2024: 100 BTC ($6.32M)
- June 26, 2024: 125 BTC ($7.71M)
- June 25, 2024: 200 BTC ($12.17M)
- June 19, 2024: 500 BTC ($32.57M)

#### Coinbase Transactions

- Coinbase: 3EHqwauPSQphJa3gYS8g8wfb3WvmazXNf3 [Link](https://explorer.bitquery.io/bitcoin/address/3EHqwauPSQphJa3gYS8g8wfb3WvmazXNf3)

![](/blog/inside-germany-bitcoin-reserves-government-holdings/coinbase.png)

Source Money Flow Movement [3EHqwauPSQphJa3gYS8g8wfb3WvmazXNf3](https://explorer.bitquery.io/bitcoin/address/3EHqwauPSQphJa3gYS8g8wfb3WvmazXNf3)

- July 12, 2024: 400 BTC ($22.90M)
- July 11, 2024: 400 BTC ($22.88M)
- July 11, 2024: 300 BTC ($17.63M)
- July 11, 2024: 375 BTC ($21.77M)
- July 10, 2024: 375 BTC ($22.03M)
- July 10, 2024: 500 BTC ($29.38M)
- July 8, 2024: 900 BTC ($51.36M)
- July 8, 2024: 600 BTC ($34.38M)
- July 8, 2024: 300 BTC ($17.13M)
- July 8, 2024: 250 BTC ($13.95M)
- July 4, 2024: 400 BTC ($23.24M)
- July 2, 2024: 100 BTC ($6.24M)
- July 1, 2024: 100 BTC ($6.32M)
- June 25, 2024: 200 BTC ($12.17M)
- June 20, 2024: 200 BTC ($13.16M)
- June 19, 2024: 500 BTC ($32.57M)

#### Flowtraders Transactions

- Flowtraders: 1FKAP8U9VABqdMgyN5s521ejEsQo8MA4uw [Link](https://explorer.bitquery.io/bitcoin/address/1FKAP8U9VABqdMgyN5s521ejEsQo8MA4uw)

![](/blog/inside-germany-bitcoin-reserves-government-holdings/flowtrades.png)

Source Money Flow Movement (1FKAP8U9VABqdMgyN5s521ejEsQo8MA4uw) using Bitquery Explorer [Link](https://explorer.bitquery.io/bitcoin/address/1FKAP8U9VABqdMgyN5s521ejEsQo8MA4uw)

- July 12, 2024: 752.17 BTC ($43.74M)
- July 11, 2024: 2,000 BTC ($114.78M)
- July 10, 2024: 3,000 BTC ($176.26M)
- July 10, 2024: 1,000 BTC ($58.75M)
- July 10, 2024: 127.38 BTC ($7.48M)
- July 8, 2024: 3,500 BTC ($200.05M)
- July 8, 2024: 700 BTC ($40.11M)
- July 8, 2024: 1,000 BTC ($57.08M)
- July 5, 2024: 547.44 BTC ($30.09M)
- July 2, 2024: 361.88 BTC ($22.65M)
- June 26, 2024: 345.34 BTC ($21.23M)

#### Cumberland DRW Transactions

- Cumberland DRW: bc1qmfqnh2urnnefnetxjdw0qjqd7gkaa080myldpn [Link](https://explorer.bitquery.io/bitcoin/address/bc1qmfqnh2urnnefnetxjdw0qjqd7gkaa080myldpn)

![](/blog/inside-germany-bitcoin-reserves-government-holdings/Cumberland.png)

Source Money Flow Movement [bc1qmfqnh2urnnefnetxjdw0qjqd7gkaa080myldpn](https://explorer.bitquery.io/bitcoin/address/bc1qmfqnh2urnnefnetxjdw0qjqd7gkaa080myldpn)

- July 12, 2024: 748.25 BTC ($42.78M)
- July 11, 2024: 100 BTC ($5.85M)
- July 11, 2024: 151.98 BTC ($8.87M)
- July 10, 2024: 536.11 BTC ($31.50M)
- July 9, 2024: 106.96 BTC ($6.16M)
- July 8, 2024: 133.72 BTC ($7.63M)

#### Bitstamp Transactions

- Bitstamp: 3M6DD5tSe7MG7uKr24PyjrfJhtaxVkbZCa [Link](https://explorer.bitquery.io/bitcoin/address/3M6DD5tSe7MG7uKr24PyjrfJhtaxVkbZCa)

![](/blog/inside-germany-bitcoin-reserves-government-holdings/Bitstamp.png)

Source Money Flow Movement [3M6DD5tSe7MG7uKr24PyjrfJhtaxVkbZCa](https://explorer.bitquery.io/bitcoin/address/3M6DD5tSe7MG7uKr24PyjrfJhtaxVkbZCa)

- July 12, 2024: 400 BTC ($22.90M)
- July 11, 2024: 700 BTC ($40.05M)
- July 11, 2024: 500 BTC ($29.38M)
- July 11, 2024: 375 BTC ($21.77M)
- July 8, 2024: 1,400 BTC ($79.90M)
- July 8, 2024: 400 BTC ($22.92M)
- July 8, 2024: 300 BTC ($17.13M)
- July 8, 2024: 250 BTC ($13.95M)
- July 4, 2024: 500 BTC ($29.05M)
- July 2, 2024: 150 BTC ($9.36M)
- July 1, 2024: 200 BTC ($12.64M)

### Real-Life Event Correlation Analysis

The timing of the German government's Bitcoin liquidation appears to be influenced by several significant real-world events, suggesting that external factors played a crucial role in the process. During the liquidation period, the European Union was actively discussing new cryptocurrency regulations, which may have pressured the government to expedite the sale before any new laws complicated the process.

Concurrently, key developments in the Movie2K legal proceedings, particularly testimony from a key witness in late June 2024, likely added urgency to the liquidation to avoid potential depreciation of the assets if the case dragged on. Additionally, the Bitcoin market experienced notable volatility in early July 2024 where it dropped over 15% from $63k, driven by macroeconomic factors, with the government's transactions coinciding with temporary price spikes—indicating an opportunistic approach to maximise returns.

## Alternative Scenarios and Fund Transfer Strategies

While the data supports a straightforward liquidation process, alternative scenarios could explain the asset movements and highlight different strategies the government may have employed:

### 1. Staggered Liquidations Across Multiple Platforms

The distribution of BTC across multiple exchanges suggests a strategy to mitigate market impact. By staggering the sales and using various platforms, the government could reduce the risk of causing a sudden price drop, ensuring a more stable market environment.

### 2. Use of Private OTC Desks

OTC desks likely played a crucial role, particularly in the transactions with Coinbase and Cumberland DRW. These desks enable large transactions to occur without affecting public order books, suggesting that a significant portion of the BTC may have been sold privately to institutional buyers.

### 3. Obfuscation Through Layered Transactions

Layered transactions, where funds are transferred through intermediary wallets before reaching exchanges, could have been employed to obscure the origins of the assets. This approach would make it more difficult for external observers to track the flow of funds, adding a layer of security to the liquidation process.

### 4. Strategic Timing for Maximum Returns

The German government’s transactions appear to have been timed to coincide with favourable market conditions, suggesting a deliberate strategy to maximise returns. This approach highlights the importance of timing in managing large-scale asset liquidations in the volatile crypto market.

---

## Conclusion: Implications for Future Seizures

The German Government sold 49,858 BTC for $2.89B - an average price of $57,900.This will serve as a benchmark in the management and sale of large-scale digital assets from legal proceedings and seizures. The careful execution, strategic timing, and collaboration with key financial institutions reflect a sophisticated approach to handling such complex transactions. As governments worldwide continue to navigate the challenges of managing seized cryptocurrencies, the lessons learned from this case will likely inform future strategies.

The sale, carried out during a time of market volatility and regulatory uncertainty, highlights the intricate relationship between regulatory actions and market behaviour. While it may have caused some short-term price swings and changes in investor sentiment, in the long run, it’s likely to lead to a stronger and more resilient cryptocurrency market, better prepared to handle large transactions and adapt to changing regulations.

–
*The information provided in this material is published solely for educational and informational purposes. It does not constitute a legal, financial audit, accounting, or investment advice. The article's content is based on the author's own research and, understanding and reasoning. The mention of specific companies, tokens, currencies, groups, or individuals does not imply any endorsement, affiliation, or association with them and is not intended to accuse any person of any crime, violation, or misdemeanor. The reader is strongly advised to conduct their own research and consult with qualified professionals before making any investment decisions. Bitquery shall not be liable for any losses or damages arising from the use of this material.*

--


_Written by Anish M_
