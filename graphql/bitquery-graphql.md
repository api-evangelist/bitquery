# Bitquery GraphQL API

Unified GraphQL endpoint for querying onchain data across 40+ supported blockchains - blocks, transactions, transfers, DEX trades, balances, holders, NFTs, prices, events, traces. Standard GraphQL POST over HTTP with API-key authentication.

## Bitquery GraphQL API (V2)

**Endpoint:** https://streaming.bitquery.io/graphql

**Documentation:** https://docs.bitquery.io/

**References:**

- Documentation: https://docs.bitquery.io/

## Bitquery GraphQL API (V1)

Legacy GraphQL endpoint covering Bitquery's V1 schema - kept for backward compatibility while customers migrate to V2.

**Endpoint:** https://graphql.bitquery.io

**Documentation:** https://docs.bitquery.io/

**References:**

- Documentation: https://docs.bitquery.io/

## Bitquery Streaming Subscriptions

GraphQL subscriptions over WebSocket for real-time blockchain data - live DEX trades, transfers, pending transactions, contract events. Same V2 schema as the HTTP endpoint, delivered as push events. Supports both `graphql-ws` and `graphql-transport-ws` subprotocols, with OAuth token passed as `?token=` on the WebSocket URL. The legacy Early Access Program endpoint `wss://streaming.bitquery.io/eap` is still honored for existing customers.

**Endpoint:** wss://streaming.bitquery.io/graphql

**Documentation:** https://docs.bitquery.io/docs/subscriptions/websockets/

**References:**

- Documentation: https://docs.bitquery.io/docs/subscriptions/websockets/
- Authentication: https://docs.bitquery.io/docs/authorisation/websocket/

## Bitquery IDE

In-browser GraphQL IDE for authoring, testing, and saving Bitquery queries against the V1 and V2 schemas. Used by developers to prototype before wiring queries into apps.

**Endpoint:** https://ide.bitquery.io/

**Documentation:** https://ide.bitquery.io/
