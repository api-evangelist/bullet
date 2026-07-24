---
name: Pull Bullet market data
description: Read live perpetuals market data from the Bullet Trading API — symbols, order book, tickers, trades, and funding.
api: openapi/bullet-trading-api-openapi.json
operations: [exchange_info, order_book, ticker_24hr, ticker_price, recent_trades, funding_rate, premium_index, open_interest]
method: generated
source: openapi/bullet-trading-api-openapi.json + https://tradingapi.bullet.xyz/docs/
---

# Pull Bullet market data

Bullet exposes Binance USD-M Futures (FAPI) compatible, public read endpoints. No authentication
is required for market data. Base host: `https://tradingapi.bullet.xyz` (testnet:
`https://tradingapi.testnet.bullet.xyz`).

## Steps

1. **Discover markets** — call `exchange_info` (`GET /fapi/v1/exchangeInfo`) to get the list of
   `symbols` (each with `marketId` and `symbol`, e.g. `BTC-USD`), assets, and chain parameters.
   Use this to map human symbols to the `marketId` values other calls need.
2. **Order book** — call `order_book` (`GET /fapi/v1/depth`) for bids/asks for a symbol.
3. **Tickers** — call `ticker_24hr` (`GET /fapi/v1/ticker/24hr`) for rolling 24h stats, or
   `ticker_price` (`GET /fapi/v1/ticker/price`) for the latest price.
4. **Recent trades** — call `recent_trades` (`GET /fapi/v1/trades`).
5. **Funding & derivatives context** — call `funding_rate` (`GET /fapi/v1/fundingRate`),
   `premium_index` (`GET /fapi/v1/premiumIndex`), and `open_interest`
   (`GET /fapi/v1/openInterest`).

## Rules

- Timestamps are **microseconds** since Unix epoch (16 digits); divide by 1,000 for Binance-style
  milliseconds. See `conventions/bullet-conventions.yml`.
- Errors return `application/json` as `{ status, message, error_id, details }`. A `404 Market not
  found` means the symbol/marketId is stale — refetch `exchange_info`. See
  `errors/bullet-problem-types.yml`.
