# Web Socket Streams for Binance (2018-11-13)
# General WSS information
* The base endpoint is: **wss://stream.binance.com:9443**
* Streams can be access either in a single raw stream or a combined stream
* Raw streams are accessed at **/ws/\<streamName\>**
* Combined streams are accessed at **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**
* Combined stream events are wrapped as follows: **{"stream":"\<streamName\>","data":\<rawPayload\>}**
* All symbols for streams are **lowercase**
* A single connection to **stream.binance.com** is only valid for 24 hours; expect to be disconnected at the 24 hour mark
* The websocket server will send a `ping frame` every 3 minutes. If the websocket server does not receive a `pong frame` back from the connection within a 10 minute period, the connection will be disconnected. Unsolicited `pong frames` are allowed.

# Detailed Stream information
## Aggregate Trade Streams
The Aggregate Trade Streams push trade information that is aggregated for a single taker order.

**Stream Name:** \<symbol\>@aggTrade

**Payload:**
```javascript
{
  "e": "aggTrade",  // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "a": 12345,       // Aggregate trade ID
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "f": 100,         // First trade ID
  "l": 105,         // Last trade ID
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
  "M": true         // Ignore
}
```

## Trade Streams
The Trade Streams push raw trade information; each trade has a unique buyer and seller.

**Stream Name:** \<symbol\>@trade

**Payload:**
```javascript
{
  "e": "trade",     // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "t": 12345,       // Trade ID
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "b": 88,          // Buyer order ID
  "a": 50,          // Seller order ID
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
  "M": true         // Ignore
}
```

## Kline/Candlestick Streams
The Kline/Candlestick Stream push updates to the current klines/candlestick every second.

**Kline/Candlestick chart intervals:**

m -> minutes; h -> hours; d -> days; w -> weeks; M -> months

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**Stream Name:** \<symbol\>@kline_\<interval\>

**Payload:**
```javascript
{
  "e": "kline",     // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "k": {
    "t": 123400000, // Kline start time
    "T": 123460000, // Kline close time
    "s": "BNBBTC",  // Symbol
    "i": "1m",      // Interval
    "f": 100,       // First trade ID
    "L": 200,       // Last trade ID
    "o": "0.0010",  // Open price
    "c": "0.0020",  // Close price
    "h": "0.0025",  // High price
    "l": "0.0015",  // Low price
    "v": "1000",    // Base asset volume
    "n": 100,       // Number of trades
    "x": false,     // Is this kline closed?
    "q": "1.0000",  // Quote asset volume
    "V": "500",     // Taker buy base asset volume
    "Q": "0.500",   // Taker buy quote asset volume
    "B": "123456"   // Ignore
  }
}
```

## Individual Symbol Mini Ticker Stream
24hr rolling window mini-ticker statistics for a single symbol pushed every second. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:** \<symbol\>@miniTicker

**Payload:**
```javascript
  {
    "e": "24hrMiniTicker",  // Event type
    "E": 123456789,         // Event time
    "s": "BNBBTC",          // Symbol
    "c": "0.0025",          // Close price
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "v": "10000",           // Total traded base asset volume
    "q": "18"               // Total traded quote asset volume
  }
```

## All Market Mini Tickers Stream
24hr rolling window mini-ticker statistics for all symbols that changed in an array pushed every second. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:** !miniTicker@arr

**Payload:**
```javascript
[
  {
    // Same as <symbol>@miniTicker payload
  }
]
```

## Individual Symbol Ticker Streams
24hr rollwing window ticker statistics for a single symbol pushed every second. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:** \<symbol\>@ticker

**Payload:**
```javascript
{
  "e": "24hrTicker",  // Event type
  "E": 123456789,     // Event time
  "s": "BNBBTC",      // Symbol
  "p": "0.0015",      // Price change
  "P": "250.00",      // Price change percent
  "w": "0.0018",      // Weighted average price
  "x": "0.0009",      // First trade(F)-1 price (first trade before the 24hr rolling window)
  "c": "0.0025",      // Last price
  "Q": "10",          // Last quantity
  "b": "0.0024",      // Best bid price
  "B": "10",          // Best bid quantity
  "a": "0.0026",      // Best ask price
  "A": "100",         // Best ask quantity
  "o": "0.0010",      // Open price
  "h": "0.0025",      // High price
  "l": "0.0010",      // Low price
  "v": "10000",       // Total traded base asset volume
  "q": "18",          // Total traded quote asset volume
  "O": 0,             // Statistics open time
  "C": 86400000,      // Statistics close time
  "F": 0,             // First trade ID
  "L": 18150,         // Last trade Id
  "n": 18151          // Total number of trades
}
```

## All Market Tickers Stream
24hr rolling window ticker statistics for all symbols that changed in an array pushed every second. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:** !ticker@arr

**Payload:**
```javascript
[
  {
    // Same as <symbol>@ticker payload
  }
]
```

## Partial Book Depth Streams
Top **\<levels\>** bids and asks, pushed every second. Valid **\<levels\>** are 5, 10, or 20.

**Stream Name:** \<symbol\>@depth\<levels\>

**Payload:**
```javascript
{
  "lastUpdateId": 160,  // Last update ID
  "bids": [             // Bids to be updated
    [
      "0.0024",         // Price level to be updated
      "10"              // Quantity
    ]
  ],
  "asks": [             // Asks to be updated
    [
      "0.0026",         // Price level to be updated
      "100"            // Quantity
    ]
  ]
}
```

## Diff. Depth Stream
Order book price and quantity depth updates used to locally manage an order book pushed every second.

**Stream Name:** \<symbol\>@depth

**Payload:**
```javascript
{
  "e": "depthUpdate", // Event type
  "E": 123456789,     // Event time
  "s": "BNBBTC",      // Symbol
  "U": 157,           // First update ID in event
  "u": 160,           // Final update ID in event
  "b": [              // Bids to be updated
    [
      "0.0024",       // Price level to be updated
      "10"            // Quantity
    ]
  ],
  "a": [              // Asks to be updated
    [
      "0.0026",       // Price level to be updated
      "100"           // Quantity
    ]
  ]
}
```

## How to manage a local order book correctly
1. Open a stream to **wss://stream.binance.com:9443/ws/bnbbtc@depth**
2. Buffer the events you receive from the stream
3. Get a depth snapshot from **https://www.binance.com/api/v1/depth?symbol=BNBBTC&limit=1000**
4. Drop any event where `u` is <= `lastUpdateId` in the snapshot
5. The first processed should have `U` <= `lastUpdateId`+1 **AND** `u` >= `lastUpdateId`+1
6. While listening to the stream, each new event's `U` should be equal to the previous event's `u`+1
7. The data in each event is the **absolute** quantity for a price level
8. If the quantity is 0, **remove** the price level
9. Receiving an event that removes a price level that is not in your local order book can happen and is normal.

