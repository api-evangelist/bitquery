---
title: Tracking Commingled Funds Simplified with Bitquery
url: https://bitquery.io/blog/tracking-commingled-funds-simplified-bitquery
date: '2024-11-13'
author: ''
feed_url: https://bitquery.io/feed
---
Think of commingled funds like mixing different-colored water in a glass. When hackers steal cryptocurrency, they often try to hide their tracks by mixing the stolen funds with legitimate transactions—just like adding clear water to colored water.

This mixing makes it challenging to identify which "drops" were originally stolen. LIBR helps us solve this problem by applying specific accounting rules to determine which portion of any withdrawal comes from the stolen funds, even after they've been mixed with other transactions. For instance, if a wallet contains both stolen and legitimate funds, LIBR helps us determine whether a specific withdrawal should be considered as coming from the stolen portion or the legitimate portion of the total balance.

In this blog, we conduct an in-depth analysis of two significant DeFi security breaches: the 2023 Euler Finance exploit and the 2024 WazirX Exchange hack. This technical analysis demonstrates how the Lowest Intermediate Balance Rule (LIBR) can be effectively applied to track stolen cryptocurrency funds.

## Understanding LIBR in Blockchain Analysis

The Lowest Intermediate Balance Rule (LIBR) is a forensic accounting methodology that Bitquery implements to track specific funds within mixed transactions. This approach is particularly valuable when analyzing cryptocurrency hacks, as it allows us to trace stolen funds even after they've been combined with other assets.

LIBR works by treating stolen funds as the first money spent from a wallet, regardless of any legitimate funds that might be mixed in. For example, if a wallet contains 100 ETH in stolen funds and 50 ETH in legitimate funds, and the wallet owner spends 120 ETH, LIBR considers the first 100 ETH of that transaction as stolen funds and only the remaining 20 ETH as coming from legitimate sources.

This principle applies even when multiple transactions occur or when funds are converted between different cryptocurrencies, making it an essential tool for tracking stolen assets across the blockchain. Think of it as a "first in, first out" system specifically designed to trace stolen cryptocurrency.

## Case Study 1: Euler Finance Exploit Analysis

The Euler Finance hack was one of the largest DeFi exploits of 2023, where attackers stole $196 million from the protocol. Here's a detailed breakdown of how the stolen funds moved:

Hacker Address: [0xb66cd966670d962c227b3eaba30a872dbfb995db](https://explorer.bitquery.io/ethereum/address/0xb66cd966670d962c227b3eaba30a872dbfb995db)

### Initial State (March 13, 2023):

- Legitimate funds in hacker's account: $0 (personal funds)
- Trust funds (stolen): $196M
- Total: $196M

### Initial Balance:

- Personal funds: $0
- Stolen funds: $196M
- Total: $196M

### First Movement ($8.5M to Tornado Cash):

- No personal funds available
- Must use stolen funds: $8.5M
- Remaining stolen funds: $187.5M

### Second Movement ($30M to Tornado Cash):

- No personal funds available
- Must use stolen funds: $30M
- Remaining stolen funds: $157.5M

**Return of Funds ($196M): - Returns the full amount of original stolen funds**

This table shows the transaction flow from the hacker's address during the Euler Finance hack.

![](/blog/tracking-commingled-funds-simplified-bitquery/table1.png)

## Case Study 2: WazirX Exchange Security Breach

The WazirX hack was a major security breach in 2024, where attackers stole $234 million from one of India's largest cryptocurrency exchanges. Following the Lowest Intermediate Balance Rule, here's a detailed breakdown of the initial fund movements:

### Initial State (July 18, 2024):

- Starting Balance (Personal funds): $0
- Stolen funds: $234M
- Total Balance: $234M
- Main Hacker: [0x04b21735E93Fa3f8df70e2Da89e6922616891a88](https://explorer.bitquery.io/ethereum/address/0x04b21735E93Fa3f8df70e2Da89e6922616891a88)

### Fund Movements Following LIBR:

First Split to Exploiter 2 ([0x35febC10112302e0d69F35F42cCe85816f8745CA](https://explorer.bitquery.io/ethereum/address/0x35febC10112302e0d69F35F42cCe85816f8745CA)):

- Balance Before: $234M (100% stolen)
- Key Token Transfers:

- 5.33 T SHIB
- 640.27 B PEPE
- 0.9 ETH
- 20.5 M MATIC
- Other tokens (FLOKI, ANKR, COTI, APE, ARB)

Second Split to Exploiter 3 ([0x90ca792206eD7Ee9bc9da0d0dF981FC5619F91Fd](https://explorer.bitquery.io/ethereum/address/0x90ca792206eD7Ee9bc9da0d0dF981FC5619F91Fd)):

- Balance Before: $231.4M (100% stolen)
- Key Token Transfers:

- 0.9 ETH
- 5.79 M USDT
- 544,818 USDC
- 135.12 M GALA
- Notable tokens: AAVE (2,523), LINK (201,643), UNI (108,231)

- Estimated Value: ~$7.5M (100% stolen funds)
- Balance After: $223.9M (100% stolen)

![](/blog/tracking-commingled-funds-simplified-bitquery/table2.png)

## Verification Through Bitquery

All analyses can be independently verified using Bitquery's APIs:

1.  WazirX Exploiter 2 Analysis Query: [Run query](https://ide.bitquery.io/WazirX-Ex-2#)
2.  WazirX Exploiter 3 Analysis Query: [Run query](https://ide.bitquery.io/WazirX-Ex-3#)

# Key Technical Insights

1.  LIBR Implementation:

- Enables precise [tracking of stolen funds](https://bitquery.io/blog/track-recover-funds-bitcoin-atm-transactions)
- Maintains accuracy through multiple transfers
- Provides verifiable transaction paths

3.  Token Distribution Analysis:

- Tracks multi-token transactions
- Monitors value equivalencies
- Documents transfer patterns

5.  Transaction Verification:

- All movements independently verifiable
- Complete transaction history available
- Real-time tracking capabilities

## Conclusion

Through Bitquery's analytical capabilities, we've demonstrated how blockchain forensics can effectively track stolen funds across complex transaction patterns. You can perform auto-tracing and complete investigation with the [Moneyflow](https://bitquery.io/products/moneyflow) tool.

The LIBR methodology, combined with our robust analytics tools, provides a comprehensive solution for monitoring and analyzing cryptocurrency exploits.

This analysis showcases the power of Bitquery's platform in:

- Implementing LIBR methodology
- Tracking multi-token transactions
- Providing verifiable transaction data
- Supporting thorough blockchain forensics

For further investigation or to conduct your own analysis, access these transactions through Bitquery's APIs.

-----

*Written by Abhishek*
