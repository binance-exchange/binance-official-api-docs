# CHANGELOG for Binance's API (2018-11-13)
## 2018-11-13
### Rest API
  * Can now cancel orders through the Rest API during a trading ban.
  * New filters: `PERCENT_PRICE`, `MARKET_LOT_SIZE`, `MAX_NUM_ICEBERG_ORDERS`.
  * Added `RAW_REQUST` rate limit. Limits based on the number of requests over X minutes regardless of weight.
  * /api/v3/ticker/price increased to weight of 2 for a no symbol query.
  * /api/v3/ticker/bookTicker increased weight of 2 for a no symbol query.
  * DELETE /api/v3/order will now return an execution report of the final state of the order.
  * `MIN_NOTIONAL` filter has two new parameters: `applyToMarket` (whether or not the filter is applied to MARKET orders) and `avgPriceMins` (the number of minutes over which the price averaged for the notional estimation). 
  * `intervalNum` added to /api/v1/exchangeInfo limits. `intervalNum` describes the amount of the interval. For example: `intervalNum` 5, with `interval` minute, means "every 5 minutes".
  
#### Explanation for the average price calculation:
  1. (qty * price) of all trades / numTrades of the trades over previous 5 minutes.

  2. If there is no trade in the last 5 minutes, it takes the first trade that happened outside of the 5min window. For example if the last trade was 20 minutes ago, that trade's price is the 5 min average.

  3. If there is no trade on the symbol, there is no average price and market orders cannot be placed.
     On a new symbol with `applyToMarket` enabled on the `MIN_NOTIONAL` filter, market orders cannot be placed until there is at least 1 trade.

  4. The current average price can be checked here: `https://api.binance.com/api/v3/avgPrice?symbol=<symbol>`
     For example:
     https://api.binance.com/api/v3/avgPrice?symbol=BNBUSDT

### User data stream
  * `Last quote asset transacted quantity` (as variable `Y`) added to execution reports. Represents the `lastPrice` * `lastQty` (`L` * `l`).

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
  *  `REQUESTS` rate limit type changed to `REQUEST_WEIGHT`. This limit was always logically request weight and the previous name for it caused confusion.

### User data stream
  *  `cummulativeQuoteQty` field added to order responses and execution reports (as variable `Z`). Represents the cummulative amount of the `quote` that has been spent (with a `BUY` order) or received (with a `SELL` order). Historical orders will have a value < 0 in this field indicating the data is not available at this time. `cummulativeQuoteQty` divided by `cummulativeQty` will give the average price for an order.
  *  `O` (order creation time) added to execution reports


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

