---
title: 'Mixers, Tumblers, and DeFi: Tracing Commingled Funds in Crypto'
url: https://bitquery.io/blog/mixers-tumblers-defi-tracing-commingled-funds-crypto
date: '2024-11-18'
author: ''
feed_url: https://bitquery.io/feed
---
Tracing commingled funds is very important on the blockchain to identify and differentiate between fraudulent and legitimate transactions. By using special processes such as LIBR(Last In, First Out—Reversed) in combination with logic and on-chain analysis, we can recognize clean funds that are associated with suspicious activities. In this article, we will cover how to identify commingled funds using these processes to track deposits and the LIBR logic to separate funds.

You can read the previous article in this series [here](https://bitquery.io/blog/tracking-commingled-funds-simplified-bitquery)

### What are commingled funds?

Commingling of funds is the mixing of digital assets from multiple sources into a single wallet or account, making it difficult to trace the sources or identify ownership of specific amounts, usually done to hide the source and destination of funds.

Funds can be commingled using the following methods;

- Mixing Services: Funds can be mixed from different sources into a single wallet through the use of [mixing or tumbling services](https://bitquery.io/blog/criminals-exploiting-crypto-2024-top-crime-typologies) that blend transactions to increase privacy. The tumbling/mixing services conceal the origin of funds, making it difficult to trace individual transactions to the source.
- Privacy Coins: Funds can be commingled through the use of privacy coins like Monero which has built-in features that allow for mixing. These privacy coins make use of cryptographic techniques that allow them to automatically commingle funds in each transaction, making it nearly impossible to trace the flow of funds.
- Centralized Exchanges: Users deposit funds into centralized exchanges, where the funds are pooled together in shared wallets, mixing assets from multiple users. This commingling creates anonymity within the exchange.
- Decentralized Finance (DeFi) Protocols: Many DeFi protocols involve the commingling of funds from multiple users. The users provide funds to a pool which creates liquidity for lending, borrowing, or trading. This aggregation makes it difficult to differentiate each user’s contribution, although the protocol often maintains records of ownership through tokens or smart contracts.

## Risks and Challenges of Commingled Funds in Blockchain

Commingling funds helps with anonymity, but it also comes with risks and challenges. These challenges are major concerns for blockchain forensic analysis, compliance, and recovering lost or stolen assets.

Let's take a look at some of the risks and challenges involved with commingled funds;

- Hidden transaction path: Commingled funds have their transaction paths scrambled, making it difficult to trace assets back to their original source, this complicates forensic and legal investigations.
- Risk of Money Laundering: [Commingled funds can be used in money laundering,](https://bitquery.io/blog/fatf-indicators-bitquery-detect-money-laundering) where money launderers use it as a means to hide funds within a series of transactions or pools, making it hard to trace the funds back to their source. This practice weakens the transparent nature of blockchain networks and also creates compliance risks for businesses that unknowingly handle these funds.
- Stolen Fund Recovery Challenges: The identification and recovery of stolen assets in commingled accounts becomes difficult, which complicates efforts for victims to recover their funds.
- Compliance: Commingled funds can present serious compliance challenges, particularly regarding Anti-Money Laundering (AML) and Know Your Customer (KYC) requirements.By mixing funds from multiple sources, commingling complicates the process of tracing fund origins, which is essential for regulatory compliance.

## Tracing using LIBR

To trace how much-misappropriated funds or irregularly transferred crypto assets remain after it has been mixed in a wallet with other deposits, we can use the Lowest Intermediate Balance Rule (LIBR) method.

Here’s a breakdown of how it works:

- The first step is to locate the wallet where the crypto was held, identify unauthorized transactions, and note each transfer’s details
- Use blockchain tools like bitquery's Explorer to trace the movement of funds to other wallets and transferred amounts.
- Note the lowest balance in the wallet after the transfer to establish the recoverable amount.
- Document transaction details for potential legal or recovery actions.

Let's take an example:

## Tracing Commingled Funds: BingX Hot Wallet Exploit

Date: September 20, 2024

Incident: Over $43 million in digital assets

Initial Balance (September 20, 2024)

- Clean Funds in Hacker's Account: $0
- Stolen Funds: $43M
- Total: $43M

![](/blog/mixers-tumblers-defi-tracing-commingled-funds-crypto/1.png)

Movements and Analysis

- The hacker converted the most of the stolen assets into 4,526 ETH and 7,864.7 BNB.

Personal Funds spent: $0

Stolen Funds spent: $43M

- A separate wallet [0x940362B46faf7DF48Af1c8989d809F50466B5fCA](https://explorer.bitquery.io/ethereum/address/0x940362B46faf7DF48Af1c8989d809F50466B5fCA/inflow?from=2024-09-19&till=2024-10-30) drained $16.5M worth of cryptocurrencies. They include;

5,300 ETH, 4,100 BNB and 1.65M MATIC.

- These funds were kept in a different wallet [0x1Dd7dAf089C16856155FeFd7e2170966bb6b3AEE](https://explorer.bitquery.io/ethereum/address/0x1Dd7dAf089C16856155FeFd7e2170966bb6b3AEE/inflow?from=2024-09-19&till=2024-10-30)

- Personal Funds Used: $0
- Stolen Funds Used: $16.5M

![](/blog/mixers-tumblers-defi-tracing-commingled-funds-crypto/2.png)

Stolen Funds Balance: $0 in primary account; $16.5M moved to another address.

#### Summary of LIBR Observations

- Total Amount Stolen: $43M
- Conversion: Stolen funds were converted to 4,526 ETH and 7,864.7 BNB to hide it.
- Extra $16.5M drained earlier was moved to another wallet address.
- All stolen funds were used, leaving no personal funds untouched.

This analysis under LIBR shows the steady depletion of stolen assets, with clear signs of concealing it through conversion and fund separation.

## Tracing Commingled Funds: WazirX Multsig Wallet Breakdown

Date: July 18, 2024

Incident: Over $230 million stolen from a multi-signature wallet

Let's take a look at one of the wallet addresses involved in the WazirX Wallet Hack on July 18, 2024;

Wallet address: [0x04b21735e93fa3f8df70e2da89e6922616891a88](https://explorer.bitquery.io/ethereum/address/0x04b21735e93fa3f8df70e2da89e6922616891a88)

Based on the [query,](https://ide.bitquery.io/Wallet-attribute-example_1) at the time of this analysis, the wallet in question has the following transaction statistics:

- The wallet has sent out a total of approximately $15,302.25, and a total transaction count of over 160.
- The total funds received in this wallet is around $15,306.69, which is recorded across 292 received transactions.
- The transactions were received from over 49 unique addresses, involving 206 different currencies.
- The wallet has sent funds to 17 unique addresses, across 137 distinct types of currencies.
- The earliest transaction recorded on this wallet was on July 18, 2024, at 6:19 am.
- The last transaction, as of this writing, was recorded on August 3, 2024, at 7:40 pm.

More details:

[Inbound transactions after July 18, 2024](https://explorer.bitquery.io/ethereum/address/0x04b21735e93fa3f8df70e2da89e6922616891a88/inflow?from=2024-07-18&till=2024-07-31),

[Outbound transactions in the same period](https://explorer.bitquery.io/ethereum/address/0x04b21735e93fa3f8df70e2da89e6922616891a88/outflow?from=2024-07-18&till=2024-07-31).

#### Deposits

![](/blog/mixers-tumblers-defi-tracing-commingled-funds-crypto/3.png)

### Withdrawals

![](/blog/mixers-tumblers-defi-tracing-commingled-funds-crypto/4.png)

#### Analysis

The deposits started on July 18, 2024, when the wallet address saw a significant inflow of ETH, SHIB, MATIC, PEPE, USDT, USDC, and other tokens. The initial deposit of 4.10 GNO is minimal when compared to other deposits. This was probably an attempt to add legitimacy to the account's activity.

This was followed by a large deposit of 15,298.16 ETH at 06:20:23 which seemed fraudulent.

Additional large deposits followed, each occurring within 12 seconds of the previous one. This precise timing suggests the deposits may have been automated to conceal their origins.

The withdrawal involved the use of numerous wallet addresses that suggest they might be exit points in the laundering. Withdrawals also involved a wide range of tokens such as BOB, LOVELY, and CHR. This diversity could indicate attempts to hide transaction history.

Key Observations:

- The large ETH and stablecoin deposits suggest the funds were fraudulent and posed a substantial risk.
- Closely timed deposits and withdrawals showed an automated system.
- The repeated withdrawals to a limited number of common wallets raise money laundering concerns.
- The use of multiple tokens, especially those with low liquidity or niche appeal, points to an effort to obscure the funds’ traceability.

Together, these factors indicate that the wallet exhibits strong signs of high-risk behavior.

### Summary of Commingled Funds Spent

- USDC: 544,818.71 (full amount spent)
- PEPE: 640.27G (full amount spent)
- USDT: 5.789M (nearly all fraudulent funds)
- ETH: 4.50 (multiple small withdrawals)

Initial Balance: $0

Stolen funds: $234M

Balance: $234M

The LIBR approach helps identify that clean GNO funds remain untouched, with fraudulent assets systematically depleted first in each withdrawal. This sequence, combined with timing and the diverse tokens used, supports suspicion of laundering, as funds are rapidly transferred using multiple low-liquidity tokens to obscure traceability

## Conclusion

Tracing commingled funds on the blockchain is an important but difficult task for maintaining transparency and security in digital asset transactions. By using processes such as LIBR alongside on-chain analysis, investigators can differentiate between clean and potentially fraudulent funds even when multiple sources are combined in a single wallet.

---

*Written by Edgar*
