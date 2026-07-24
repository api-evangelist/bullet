---
name: Monitor a Bullet account
description: Query account state, balances, positions, open orders, and fills on the Bullet Trading API.
api: openapi/bullet-trading-api-openapi.json
operations: [account_info, account_balance, account_config, query_open_orders, query_open_order, all_orders, user_trades, commission_rate, leverage_bracket]
method: generated
source: openapi/bullet-trading-api-openapi.json + https://tradingapi.bullet.xyz/docs/
---

# Monitor a Bullet account

Read account, position, and order state for a trading account. Base host:
`https://tradingapi.bullet.xyz`. Account queries are read endpoints; the write path (placing and
cancelling orders) is done via ed25519-signed transactions — see
`authentication/bullet-authentication.yml`.

## Steps

1. **Account snapshot** — call `account_info` (`GET /fapi/v3/account`) for account state and
   positions, and `account_balance` (`GET /fapi/v3/balance`) for balances. `account_config`
   (`GET /fapi/v1/accountConfig`) returns per-account configuration.
2. **Open orders** — call `query_open_orders` (`GET /fapi/v1/openOrders`) for all resting orders,
   or `query_open_order` (`GET /fapi/v1/openOrder`) for one.
3. **History (Beta)** — call `all_orders` (`GET /fapi/v1/allOrders`) and `user_trades`
   (`GET /fapi/v1/userTrades`). These are cursor-paginated: pass `limit`, `startTime`, `endTime`,
   and `cursor`. See `conventions/bullet-conventions.yml`.
4. **Fees & risk** — call `commission_rate` (`GET /fapi/v1/commissionRate`) and `leverage_bracket`
   (`GET /fapi/v1/leverageBracket`).

## Rules

- Cancels can reference the optional user-defined `client_order_id` (u64) supplied at order time.
- On `401 Invalid Solana signature`, re-check the signing keypair / delegate-account key before
  retrying any transaction.
