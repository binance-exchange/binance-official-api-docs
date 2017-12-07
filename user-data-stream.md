# User Data Streams for Binance (2017-12-01)
# General WSS information
* The base API endpoint is: **https://api.binance.com**
* A User Data Stream `listenKey` is valid for 60 minutes after creation.
* Doing a `PUT` on a `listenKey` will extend its validity for 60 minutes.
* Doing a `DELETE` on a `listenKey` will close the stream.
* The base websocket endpoint is: **wss://stream.binance.com:9443**
* User Data Streams are accessed at **/ws/\<listenKey\>**
* A single connection to **stream.binance.com** is only valid for 24 hours; expect to be disconnected at the 24 hour mark.

# API Endpoints
## Create a listenKey
```
POST /api/v1/userDataStream
```
Start a new user data stream. The stream will close after 60 minutes unless a keepalive is sent.

**Weight:**
1

**Parameters:**
NONE

**Response:**
```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

## Ping/Keep-alive a listenKey
```
PUT /api/v1/userDataStream
```
Keepalive a user data stream to prevent a time out. User data streams will close after 60 minutes. It's recommended to send a ping about every 30 minutes.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES

**Response:**
```javascript
{}
```

## Close a listenKey
```
DELETE /api/v1/userDataStream
```
Close out a user data stream.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES

**Response:**
```javascript
{}
```

# Web Socket Payloads
## Account Update
Account state is updated with the `outboundAccountInfo` event.

**Payload:**
```javascript
{
  "e": "outboundAccountInfo",   // Event type
  "E": 1499405658849,           // Event time
  "m": 0,                       // Maker commission rate (bips)
  "t": 0,                       // Taker commission rate (bips)
  "b": 0,                       // Buyer commission rate (bips)
  "s": 0,                       // Seller commission rate (bips)
  "T": true,                    // Can trade?
  "W": true,                    // Can withdraw?
  "D": true,                    // Can deposit?
  "B": [                        // Balances array
    {
      "a": "LTC",               // Asset
      "f": "17366.18538083",    // Free amount
      "l": "0.00000000"         // Locked amount
    },
    {
      "a": "BTC",
      "f": "10537.85314051",
      "l": "2.19464093"
    },
    {
      "a": "ETH",
      "f": "17902.35190619",
      "l": "0.00000000"
    },
    {
      "a": "BNC",
      "f": "1114503.29769312",
      "l": "0.00000000"
    },
    {
      "a": "NEO",
      "f": "0.00000000",
      "l": "0.00000000"
    }
  ]
}
```

## Order Update
Orders are updated with the `executionReport` event. Check the API documentation and below for relevant enum definitions.

**Payload:**
```javascript
{
  "e": "executionReport",        // Event type
  "E": 1499405658658,            // Event time
  "s": "ETHBTC",                 // Symbol
  "c": "mUvoqJxFIILMdfAW5iGSOW", // Client order ID
  "S": "BUY",                    // Side
  "o": "LIMIT",                  // Order type
  "f": "GTC",                    // Time in force
  "q": "1.00000000",             // Order quantity
  "p": "0.10264410",             // Order price
  "P": "0.00000000",             // Ignore
  "F": "0.00000000",             // Iceberg quantity
  "g": -1,                       // Ignore
  "C": "null",                   // Ignore
  "x": "NEW",                    // Current execution type
  "X": "NEW",                    // Current order status
  "r": "NONE",                   // Order reject reason; will be an error code.
  "i": 4293153,                  // Order ID
  "l": "0.00000000",             // Last executed quantity
  "z": "0.00000000",             // Cumulative filled quantity
  "L": "0.00000000",             // Last executed price
  "n": "0",                      // Commission amount
  "N": null,                     // Commission asset
  "T": 1499405658657,            // Transaction time
  "t": -1,                       // Trade ID
  "I": 8641984,                  // Ignore
  "w": true,                     // Is the order working?
  "m": false,                    // Is this trade the maker side?
  "M": false                     // Ignore
}
```

**Execution types:**

* NEW
* CANCELED
* REPLACED (currently unused)
* REJECTED
* TRADE
* EXPIRED

