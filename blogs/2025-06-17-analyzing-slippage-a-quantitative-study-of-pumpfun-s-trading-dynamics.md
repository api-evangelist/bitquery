---
title: 'Analyzing Slippage: A Quantitative Study of Pumpfun’s Trading Dynamics'
url: https://bitquery.io/blog/analyzing-slippage-pumpfun-trading-dynamics
date: '2025-06-17'
author: ''
feed_url: https://bitquery.io/feed
---
Have you ever sent 1 SOL on Pumpfun only to receive far fewer tokens than you expected? That unwelcome surprise is known as slippage: the hidden cost of executing fast trades in volatile markets. In decentralized finance, slippage refers to the gap between the price you anticipate when submitting a swap and the actual price at which it executes. Even small differences measured in basis points (1 bp = 0.01%) can add up, especially when trading memecoins on a busy launchpad like Pumpfun.

Pumpfun operates on [Solana](https://docs.bitquery.io/docs/category/solana/) as a memecoin launchpad where token prices automatically rise along a bonding curve each time buyers pour in. Its popularity means liquidity can shift rapidly, and markets move at breakneck speed. In such an environment, slippage is not just likely but almost guaranteed: a modest buy order can push price upward mid-transaction, and if bots or validators detect your pending trade, they may front-run it, further worsening execution. Conversely, under certain conditions, you might even see “negative slippage,” where price moves in your favor between quote and execution, but that is less common for typical users.

To shed light on these dynamics, we analyzed a dataset of 5,000 Pumpfun DEX trades spanning nine tokens, complete with timestamps, trade sizes, quoted and realized prices, and computed slippage. Our goal is to help you understand how slippage is calculated, how often it occurs, and what patterns emerge in this high-volatility setting. By examining which trades experienced zero slippage, which suffered the worst-case losses, and when front-running behavior seems to appear, you’ll gain insight into the hidden mechanics affecting your orders.

We compute slippage in basis points using the formula:

```
slippage (bps) = ((realizedPrice / quotedPrice) – 1) × (–10000)
```

Where,

- realizedPrice: The realized execution price of the swap (the amount of the input asset spent, over the amount of the output asset received).
- quotedPrice: The decision price shown to the user. For this, we can query the price of the previous trade of the pumpfun token pair

A positive slippage value indicates you received a worse price than quoted (you “lost” relative to expectation), while a negative value indicates a better price (you “gained”). This convention makes interpretation straightforward: positive means loss, negative means gain.

Front-running bots magnify slippage in Pumpfun’s fast-moving pools. By observing pending transactions in the mempool and placing their orders ahead of or behind yours (“sandwich” attacks), they can push price against your trade, resulting in higher slippage. Understanding these tactics and the underlying formula helps you set appropriate slippage tolerances, choose safer trade sizes, and consider mitigation strategies such as private transaction submission or splitting orders.

In the sections that follow, we’ll walk through slippage concepts in simple terms and illustrate how we calculated slippage on a real Pumpfun dataset, presented in clear charts and tables. You’ll see how often zero slippage occurs, which tokens show the most stability, when and why traders suffer large losses, and how front-running behavior manifests in our dataset. Armed with this knowledge, you can trade smarter on Pumpfun and avoid nasty surprises.

### How PumpFun Works and Why Slippage Is Built In

[Pumpfun](https://docs.bitquery.io/docs/examples/Solana/Pump-Fun-API/) is a memecoin launchpad built on Solana where tokens follow a [bonding curve](https://docs.bitquery.io/docs/examples/Solana/Pump-Fun-Marketcap-Bonding-Curve-API/) pricing model. That means every time someone buys a token, its price goes up automatically, not based on traditional supply and demand but based on a mathematical formula.

This system creates an extremely fast-moving environment. A few users buying at once can push the price up while your transaction is still processing. So even if you saw a good price when you submitted the trade, by the time it goes through, you might be buying at a much higher price. That’s slippage.

### The Role of Bots and Front-Running

Now here’s where it gets a bit more complex and more dangerous for traders.

When you submit a transaction, it enters something called the mempool a temporary holding area for unconfirmed transactions. In many blockchain systems, the mempool is public. That means bots and validators can see your pending trade before it executes.

Some bots exploit this by performing what's called a front-running attack. They jump in ahead of your trade, pushing the price up before yours goes through. Even sneakier bots use a strategy called a sandwich attack, placing one trade before and one after yours, essentially boxing you in and draining value from your transaction. This kind of manipulation is part of what’s called [MEV (Maximal Extractable Value)](https://bitquery.io/blog/different-mev-attacks), the profit that bots or validators can extract by reordering transactions.

## Dataset Overview

The dataset contains 5,000 Pumpfun token trades obtained using [Bitquery Pumpfun Trade Streams](https://docs.bitquery.io/docs/examples/Solana/Pump-Fun-API/#get-real-time-trades-on-pump-fun) over about 11 minutes (June 9, 2025). Key columns include Time (UTC), Token Name, Amount (token units), Amount (USD), Price (USD) (quoted price), Side Type (buy/sell), Side Amount (token), Side Amount (USD), and Slippage (bps).

After filtering anomalies (trades with quoted price = 0 leading to undefined slippage), we have 4,844 valid trades across 9 tokens: catwifmask, costco, THE AMERICA PARTY, KILL BIG BEAUTIFUL BILL, Kill Bill, moonpig, AMERICA PARTY, OFFICIAL MUSK, and President Vs Elon. These trades span several DEX protocols, mostly Pumpfun’s own AMM and Liquid Blok ClmM, plus Whirlpool and others.

## Slippage Distribution

Most trades saw small slippage. In our data (excluding anomalies), the median slippage is 0 bps, and the 75th percentile is about +6 bps, meaning three-quarters of trades lost under 6 bps (or gained under 6 bps) relative to the quote. The distribution is roughly symmetric: about 8% of trades had zero slippage, ~47% had positive slippage (trader got a worse deal), and ~45% had negative slippage (trader got a better deal). (For context, a large 2021 study similarly found about 50% zero slippage and 33% negative slippage, though exact splits vary by dataset.)

Slippage is highly skewed: a few trades suffer enormous slippage, but most are small. Over half of non-zero-slippage trades had |slippage| < 10 bps. Only about 2% of trades experienced slippage above 100 basis points. The extreme tail includes 11 trades above +1000 bps (worst losses) and 6 trades below –1000 bps (huge gains). In particular, our top slippage trades exceeded 5000 bps (see next section). Overall, the distribution underscores that tiny or moderate trades have minimal slippage, while illiquid or exploited trades can experience massive deviations.

## How Many Zero and Negative Slippages?

From 4,844 valid trades:

- Zero slippage: 406 trades (~8%). These occurred when the pool price changed negligibly between the quote and execution.
- Negative slippage (favorable): 2,294 trades. Traders got slightly better prices, often due to other trades shifting prices in their favor after quoting but before execution.
- Positive slippage (loss): 2,144 trades. Traders paid slightly more or received slightly less.

Why many favorable slippages?

In a live DEX, price moves both ways. After you submit a transaction, other trades might move the price beneficially before execution. Also, bots sometimes push prices up/down around your trade, yielding short windows where later trades see a better price.

## Worst Slippage Trades

The top 5 trades with the worst slippage (highest positive bps) are all for catwifmask. They are summarized below, listing the timestamp (UTC), token, side (buy/sell), USD size, and slippage:

![](/blog/analyzing-slippage-pumpfun-trading-dynamics/table.png)

Interpretation: For these trades, slippage > +5000 bps means price deviated by >50% relative to the quoted price. Such extreme losses typically happen in illiquid pools when a trade moves price drastically, or when bots sandwich small trades.

These tiny to modest-sized trades (all <$500) suffered catastrophic slippage, indicating severe price impact or possible exploitation. (All 10000 bps slippage cases were due to data glitches with price=0 and are omitted above.)

### Most Stable Tokens

Based on the average absolute slippage (mean of |bps|) per token, the four most stable tokens in our dataset were:

- moonpig (mean |slippage| ≈ 14.5 bps): Trades in this pool experienced very small price deviations on average. Most slippage events remained within ±50 bps (±0.50%). Such consistency indicates healthy liquidity and balanced trading activity, making Moonpig relatively predictable for traders.
- President Vs Elon (mean |slippage| ≈ 17.1 bps): Although there were fewer trades for this token, the observed slippage remained mild. This suggests that even with lower volume, price shifts between consecutive trades were modest.
- OFFICIAL MUSK (mean |slippage| ≈ 26.5 bps): Moderate average deviations, generally within ±100 bps. While occasional spikes occurred, they were infrequent, indicating that traders could usually execute without a drastic price impact.
- Costco (mean |slippage| ≈ 31.2 bps): Despite a larger number of trades, the pool maintained relatively low slippage overall (around ±0.3% on average). This reflects a balance of supply and demand or sufficient deep liquidity for routine trades.

In contrast, tokens like catwifmask exhibited much higher mean absolute slippage (~62 bps) and severe outliers, indicating that trades there often faced large price swings or potential MEV activity. Other tokens (e.g., AMERICA PARTY, THE AMERICA PARTY, Kill Bill) showed average slippage near zero but with occasional spikes, suggesting intermittent liquidity stress or concentrated trading bursts

Choosing tokens with low average slippage reduces the likelihood of unexpectedly large execution costs. For tokens like Moonpig or Costco, traders can set tighter slippage tolerances and expect more predictable outcomes. Conversely, high-slippage pools require extra caution: smaller order sizes, higher tolerance for potential price impact, or avoidance unless liquidity improves

![](/blog/analyzing-slippage-pumpfun-trading-dynamics/chart.png)
Fig: Mean Absolute Slippage by Token

### Slippage Over Time

When slippage is aggregated over time (e.g., averaged per minute), we observe that most time intervals exhibit average slippage close to zero, punctuated by brief spikes. In our 11-minute snapshot:

- Stable intervals: During most minutes, the minute-level average slippage hovers around 0 bps. This indicates that for routine trades in stable or liquid pools, price changes between consecutive transactions were minimal.
- Spikes aligned with large or illiquid-token trades: Certain minutes showed noticeable positive or negative average slippage, often corresponding to clusters of trades in low-liquidity pools (notably catwifmask). For example, when a sizable trade or a sequence of trades hit a shallow pool, the subsequent trades in that same minute experienced larger deviations, pulling the average slippage for that period away from zero.
- Implications for traders: Monitoring slippage trends over time can help identify moments of heightened risk. If you see a recent spike in average slippage for a token, it likely signals that liquidity has been disturbed, perhaps by large orders or MEV activity, and you may want to delay your trade, reduce size, or widen your slippage tolerance cautiously.

Over the 11-minute window, slippage trends were mostly calm until two bursts: around 09:51:17–09:51:33 UTC, many catwifmask trades were executed with enormous slippage, pushing the minute-average slippage above +10 bps. For instance, at 09:51, the mean slippage was +18.5 bps (due to the large catwifmask losses). Before and after this, per-minute averages hovered near 0 or slightly negative. Overall, slippage spiked whenever big trades occurred in low-liquidity tokens.

## Token-by-Token Insights

catwifmask: By far the most volatile. Median slippage ≈was 0, but it had the highest extremes (both +5000 bps and 1400 bps). A series of catwifmask trades suffered a massive impact, suggesting very low liquidity or targeted attacks.

Costco: The largest sample (898 trades) with generally small slippage. Average slippage slightly negative (~–0.7 bps), indicating gains on average (perhaps due to routine small advantageous fills). Outliers reached +988 bps or –1158 bps.

THE AMERICA PARTY: Lots of trades (717) with median ≈0. Mean slippage is slightly negative (good). This token had some big swings (±1100 bps), but those were rare.

AMERICA PARTY: Fewer trades (70), mostly small, with occasional spikes (max ~+402 bps, min –237 bps). Average close to neutral.

KILL BIG BEAUTIFUL BILL: Fairly active (406 trades), median slippage 0, mean about –0.8 bps (slightly favorable). It experienced significant swings (over +1800 bps and –2190 bps), but the typical result was mild.

Kill Bill: Moderate activity (193 trades). Mean slippage +0.25 bps (slightly adverse). Volatility was moderate (up to ±900+ bps).

moonpig: Few trades (150) but very smooth. Almost all slippages were under ±50 bps, with none above ±200. The average was near zero.

OFFICIAL MUSK: Small sample (50 trades). Mean +3.1 bps (somewhat adverse), with moderate extremes (~±130 bps).

President Vs Elon: Very few trades (32) but interestingly mean 5.9 bps (on average favorable), with extremes ±60 bps. This suggests occasional nice fills or luck.

## Front-Running Patterns

There are hints of MEV-style sandwich trades. For example, around 09:42:55 UTC in the catwifmask pool, a tiny buy (effectively 0 USD) suffered +4711 bps slippage, immediately followed by a moderate buy (≈$72) with –8951 bps slippage (huge gain), and then another similar-size trade with +3682 bps slippage. This pattern, a tiny trade getting drained, a block-level trade getting a far-better-than-quoted price, and a following trade hitting a poor price, is consistent with a classic sandwich attack (bots arbitraging around a target trade). Similarly, at 09:51:17 UTC, four tiny buys preceded a large sell, which took the brunt of the price movement. In both cases, no normal “preceding large trade” explains the slippage; rather, it looks like small trades or bots pushed the price. While our data lacks explicit trader addresses to confirm, these anomalies strongly suggest front-running by bots is affecting some Pumpfun trades.

## Key Findings

- Worst slippage trades : The absolute losers (highest slippage) were all catwifmask trades (see table above). They lost on the order of +5000 bps.

- Zero slippage count : 406 trades had exactly 0 bps slippage. These likely occurred when liquidity and timing aligned perfectly (or trades were immediately offset).
- Negative slippage (favorable trades) : 2,294 trades had negative slippage (the trader got a better price), versus 2,144 with positive slippage, out of 4,844 valid trades. (This minor imbalance mirrors observations that ~33% of trades can get negative slippage, often due to MEV dynamics.)
- Most stable tokens : moonpig, President Vs Elon, OFFICIAL MUSK, and Costco had the lowest average slippage (mostly single-digit bps). These tokens showed the least volatility.
- Trader consistency : We cannot identify individual wallet addresses or routers from this dataset (no address fields), so we cannot name traders who consistently gained or lost. However, the data does not indicate any one “privileged” address: slippage outcomes seem determined by token liquidity and timing rather than a single repeat winner. Various DEX venues (Pumpfun AMM, LB CLMM, etc.) were used, with no obvious router bias.

- Repeated favorable pricing : Likewise, without address or router info, we cannot conclusively say if certain participants repeatedly got better pricing. The fact that many tokens had near-zero median slippage suggests no clear systematic advantage; the large negative-slippage outliers we see are likely opportunistic bot trades, not regular end-users.

## Conclusion

Slippage is a fundamental aspect of DeFi trading. Our analysis of Pumpfun DEX trades shows:

- Most trades experienced small slippage within ±10 bps.
- Approximately equal proportions saw slight gains or losses relative to the quoted price.
- Some tokens exhibited stable slippage, while others showed extreme volatility and possible MEV exploitation.
- Beginners should prioritize liquidity assessment, proper slippage tolerance, and MEV mitigation for safer trading.

Understanding how slippage occurs and learning to interpret slippage metrics empowers traders to navigate decentralized markets more confidently. Always remain vigilant, especially with new or thinly traded tokens, and leverage available tools to reduce adverse outcomes

In summary, this Pumpfun trade snapshot shows mostly modest slippage for standard trades, but with dramatic exceptions. Traders should set appropriate slippage tolerances and watch for MEV attacks: as others have noted, “active participants [can] trade against you, and thus you will experience negative slippage”, so vigilance is key when trading new or illiquid tokens.

---

_This material is for educational and informational purposes only and is not intended as investment advice. The content reflects the author's personal research and understanding. While specific investments and strategies are mentioned, no endorsement or association with these entities is implied. Readers should conduct their own research and consult with qualified professionals before making any investment decisions. Bitquery is not liable for any losses or damages resulting from the application of this information._

---

Written by Dhimahi
