# CHANGELOG for Binance's API (2018-07-18)
## 2018-07-18
### Rest API
  *  New filter: `ICEBERG_PARTS`
  *  `POST api/v3/order` new defaults for `newOrderRespType`. `ACK`, `RESULT`, or `FULL`; `MARKET` and `LIMIT` order types default to `FULL`, all other orders default to `ACK`.
  *  POST api/v3/order `RESULT` and `FULL` responses now have `cummulativeQuoteQty`
  *  GET api/v3/openOrders with no symbol weight reduced to 40.
  *  GET api/v3/ticker/24hr with no symbol weight reduced to 40.
  *  Max amount of trades from GET /api/v1/trades increased to 1000.
  *  Max amount of trades from GET /api/v1/historicalTrades increased to 1000.
  *  Max amount of aggregate trades from GET /api/v1/aggTrades increased to 1000.
  *  Max amount of aggregate trades from GET /api/v1/klines increased to 1000.
  *  Rest API Order lookups now return `updateTime` which represents the last time the order was updated; `time` is the order creation time.
  *  Order lookup endpoints will now return `cummulativeQuoteQty`. If `cummulativeQuoteQty` is < 0, it means the data isn't available for this order at this time.

### User data stream
  *  `cummulativeQuoteQty` field added to order responses and execution reports (as variable `Z`). Represents the cummulative amount of the `quote` that has been spent (with a `BUY` order) or received (with a `SELL` order). Historical orders will have a value < 0 in this field indicating the data is not available at this time. `cummulativeQuoteQty` divided by `cummulativeQty` will give the average price for an order.
  *  "O" (order creation time) added to execution reports


## 2018-01-23
  * GET /api/v1/historicalTrades weight decreased to 5
  * GET /api/v1/aggTrades weight decreased to 1
  * GET /api/v1/klines weight decreased to 1
  * GET /api/v1/ticker/24hr all symbols weight decreased to number of trading symbols / 2
  * GET /api/v3/allOrders weight decreased to 5
  * GET /api/v3/myTrades weight decreased to 5
  * GET /api/v3/account weight decreased to 5
  * GET /api/v1/depth limit=500 weight decreased to 5
  * GET /api/v1/depth limit=1000 weight decreased to 10
  * -1003 error message updated to direct users to websockets

## 2018-01-20
  * GET /api/v1/ticker/24hr single symbol weight decreased to 1
  * GET /api/v3/openOrders all symbols weight decreased to number of trading symbols / 2
  * GET /api/v3/allOrders weight decreased to 15
  * GET /api/v3/myTrades weight decreased to 15
  * GET /api/v3/order weight decreased to 1
  * myTrades will now return both sides of a self-trade/wash-trade

## 2018-01-14
  * GET /api/v1/aggTrades weight changed to 2
  * GET /api/v1/klines weight changed to 2
  * GET /api/v3/order weight changed to 2
  * GET /api/v3/allOrders weight changed to 20
  * GET /api/v3/account weight changed to 20
  * GET /api/v3/myTrades weight changed to 20
  * GET /api/v3/historicalTrades weight changed to 20

