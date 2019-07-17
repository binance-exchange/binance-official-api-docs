# Public Rest API for Margin Trade
# General API Information
* The base endpoint is: **https://api.binance.com**
* All endpoints return either a JSON object or array.
* Data is returned in **ascending** order. Oldest first, newest last.
* All time and timestamp related fields are in milliseconds.
* HTTP `4XX` return codes are used for for malformed requests;
  the issue is on the sender's side.
* HTTP `429` return code is used when breaking a request rate limit.
* HTTP `418` return code is used when an IP has been auto-banned for continuing to send requests after receiving `429` codes.
* HTTP `5XX` return codes are used for internal errors; the issue is on
  Binance's side.
  It is important to **NOT** treat this as a failure operation; the execution status is
  **UNKNOWN** and could have been a success.
* Any endpoint can return an ERROR; the error payload is as follows:
```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* Specific error codes and messages defined in another document.
* For `GET` endpoints, parameters must be sent as a `query string`.
* For `POST`, `PUT`, and `DELETE` endpoints, the parameters may be sent as a
  `query string` or in the `request body` with content type
  `application/x-www-form-urlencoded`. You may mix parameters between both the
  `query string` and `request body` if you wish to do so.
* Parameters may be sent in any order.
* If a parameter sent in both the `query string` and `request body`, the
  `query string` parameter will be used.


# Endpoint security type
* Each endpoint has a security type that determines the how you will
  interact with it.
* API-keys are passed into the Rest API via the `X-MBX-APIKEY`
  header.
* API-keys and secret-keys **are case sensitive**.
* API-keys can be configured to only access certain types of secure endpoints.
 For example, one API-key could be used for TRADE only, while another API-key
 can access everything except for TRADE routes.
* By default, API-keys can access all secure routes.

Security Type | Description
------------ | ------------
NONE | Endpoint can be accessed freely.
TRADE | Endpoint requires sending a valid API-Key and signature.
USER_DATA | Endpoint requires sending a valid API-Key and signature.
MARGIN | Endpoint requires sending a valid API-Key and signature.
USER_STREAM | Endpoint requires sending a valid API-Key.
MARKET_DATA | Endpoint requires sending a valid API-Key.


* `TRADE` and `USER_DATA` endpoints are `SIGNED` endpoints.

# SIGNED (TRADE and USER_DATA) Endpoint security
* `SIGNED` endpoints require an additional parameter, `signature`, to be
  sent in the  `query string` or `request body`.
* Endpoints use `HMAC SHA256` signatures. The `HMAC SHA256 signature` is a keyed `HMAC SHA256` operation.
  Use your `secretKey` as the key and `totalParams` as the value for the HMAC operation.
* The `signature` is **not case sensitive**.
* `totalParams` is defined as the `query string` concatenated with the
  `request body`.

## Timing security
* A `SIGNED` endpoint also requires a parameter, `timestamp`, to be sent which
  should be the millisecond timestamp of when the request was created and sent.
* An additional parameter, `recvWindow`, may be sent to specify the number of
  milliseconds after `timestamp` the request is valid for. If `recvWindow`
  is not sent, **it defaults to 5000**.
* The logic is as follows:
  ```javascript
  if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow) {
    // process request
  } else {
    // reject request
  }
  ```

**Serious trading is about timing.** Networks can be unstable and unreliable,
which can lead to requests taking varying amounts of time to reach the
servers. With `recvWindow`, you can specify that the request must be
processed within a certain number of milliseconds or be rejected by the
server.


**It recommended to use a small recvWindow of 5000 or less!**


## SIGNED Endpoint Examples for POST /api/v1/order
Here is a step-by-step example of how to send a vaild signed payload from the
Linux command line using `echo`, `openssl`, and `curl`.

Key | Value
------------ | ------------
apiKey | vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A
secretKey | NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j


Parameter | Value
------------ | ------------
symbol | LTCBTC
side | BUY
type | LIMIT
timeInForce | GTC
quantity | 1
price | 0.1
recvWindow | 5000
timestamp | 1499827319559


### Example 1: As a query string
* **queryString:** symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559
* **HMAC SHA256 signature:**

    ```
    [linux]$ echo -n "symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
    ```


* **curl command:**

    ```
    (HMAC SHA256)
    [linux]$ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://api.binance.com/api/v1/order?symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
    ```

### Example 2: As a request body
* **requestBody:** symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559
* **HMAC SHA256 signature:**

    ```
    [linux]$ echo -n "symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
    ```


* **curl command:**

    ```
    (HMAC SHA256)
    [linux]$ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://api.binance.com/api/v1/order' -d 'symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
    ```

### Example 3: Mixed query string and request body
* **queryString:** symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC
* **requestBody:** quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559
* **HMAC SHA256 signature:**

    ```
    [linux]$ echo -n "symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTCquantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= 0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77
    ```


* **curl command:**

    ```
    (HMAC SHA256)
    [linux]$ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://api.binance.com/api/v1/order?symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC' -d 'quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77'
    ```

Note that the signature is different in example 3.
There is no & between "GTC" and "quantity=1".



## General endpoints
### Margin account transfer (MARGIN)
```
Post /sapi/v1/margin/transfer 
```
Execute transfer between spot account and margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | The asset being transferred, e.g., BTC
amount | DECIMAL | YES | The amount to be transferred
type | INT | YES | 1: transfer from main account to margin account 2: transfer from margin account to main account



**Response:**
```javascript
{
    //transaction id
    "tranId": 100000001
}
```

### Margin account borrow (MARGIN)
```
Post /sapi/v1/margin/loan 
```
Apply for a loan.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
amount | DECIMAL | YES | 


**Response:**
```javascript
{
    //transaction id
    "tranId": 100000001
}
```

### Margin account repay (MARGIN)
```
Post /sapi/v1/margin/repay
```
Repay loan for margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
amount | DECIMAL | YES | 

**Response:**
```javascript
{
    //transaction id
    "tranId": 100000001
}
```

### Margin account new order (TRADE)
```
Post  /sapi/v1/margin/order
```
Post a new order for margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side |	ENUM |YES |	BUY<br>SELL
type | ENUM | YES	
quantity | DECIMAL |	YES	
price |	DECIMAL | NO	
stopPrice | DECIMAL | NO | Used with STOP_LOSS, STOP_LOSS_LIMIT, TAKE_PROFIT, and TAKE_PROFIT_LIMIT orders.
newClientOrderId | STRING | NO | A unique id for the order. Automatically generated if not sent.
icebergQty | DECIMAL | NO | Used with LIMIT, STOP_LOSS_LIMIT, and TAKE_PROFIT_LIMIT to create an iceberg order.
newOrderRespType | ENUM | NO | Set the response JSON. ACK, RESULT, or FULL; MARKET and LIMIT order types default to FULL, all other orders default to ACK.
timeInForce | ENUM | NO | GTC,IOC,FOK


**Response ACK:**
```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595
}
```
**Response RESULT:**
```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595,
  "price": "1.00000000",
  "origQty": "10.00000000",
  "executedQty": "10.00000000",
  "cummulativeQuoteQty": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "MARKET",
  "side": "SELL"
}
```
**Response FULL:**
```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595,
  "price": "1.00000000",
  "origQty": "10.00000000",
  "executedQty": "10.00000000",
  "cummulativeQuoteQty": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "MARKET",
  "side": "SELL",
  "fills": [
    {
      "price": "4000.00000000",
      "qty": "1.00000000",
      "commission": "4.00000000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3999.00000000",
      "qty": "5.00000000",
      "commission": "19.99500000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3998.00000000",
      "qty": "2.00000000",
      "commission": "7.99600000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3997.00000000",
      "qty": "1.00000000",
      "commission": "3.99700000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3995.00000000",
      "qty": "1.00000000",
      "commission": "3.99500000",
      "commissionAsset": "USDT"
    }
  ]
}
```

### Margin account cancel order (TRADE)
```
Delete /sapi/v1/margin/order
```
Cancel an active order for margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO | 
origClientOrderId |	STRING | NO	
newClientOrderId |	STRING | NO | Used to uniquely identify this cancel. Automatically generated by default.

Either orderId or origClientOrderId must be sent.

**Response:**
```javascript
{
  "symbol": "LTCBTC",
  "orderId": 28,
  "origClientOrderId": "myOrder1",
  "clientOrderId": "cancelMyOrder1",
  "transactTime": 1507725176595,
  "price": "1.00000000",
  "origQty": "10.00000000",
  "executedQty": "8.00000000",
  "cummulativeQuoteQty": "8.00000000",
  "status": "CANCELED",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "SELL"
}
```

### Query loan record (USER_DATA)
```
Get /sapi/v1/margin/query/loan
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset |	STRING | YES	
txId | LONG | NO | the tranId in POST /sapi/v1/margin/loan
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | Current page number.
size |	LONG | NO |	Per page row size.

TxId or [startTime, endTime) must be sent. txId takes precedence.

**Response:**
```javascript
{
  "rows": [
    {
      "asset": "BNB",
      "principal": 11.34,
      "timestamp": 1555056425,
      //one of PENDING (pending to execution), COMPLETE (executed, waiting to be confirmed), CONFIRM (successfully loaned), FAILED (execution failed, nothing happened to your account);
      "status": "CONFIRM"
    }
  ],
  "total": 1
}
```

### Query repay record (USER_DATA)
```
Get /sapi/v1/margin/query/repay
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
sset | STRING |	YES	
txId | LONG | NO | return of /sapi/v1/margin/repay 
startTime | LONG | NO	
endTime | LONG | NO	
current | LONG | NO	
size | LONG | NO

TxId or [startTime, endTime) must be sent. txId takes precedence. 


**Response:**
```javascript
{
  "rows": [
    {
      "asset": "BNB",
      //Total amount repaid
      "amount": 11.35,
      //Principal repaid
      "principal": 11.34,
      //Interest repaid
      "interest": 0.01,
      "timestamp": 1555056425,
      //one of PENDING (pending to execution), COMPLETE (executed, waiting to be confirmed), CONFIRM (successfully loaned), FAILED (execution failed, nothing happened to your account);
      "status": "CONFIRM"
    }
  ],
  "total": 1
}
```

### Query margin account details (USER_DATA)
```
Get /sapi/v1/margin/query/account
```

**Weight:**
5

**Parameters:**

None

**Response:**
```javascript
{
   "marginLevel":999,
   "totalAssetOfBtc":225.53915746,
   "totalLiabilityOfBtc":0.0823434408,
   "totalNetAssetOfBtc":225.4568140192,
   "tradeEnabled":true,
   "userAssets":[
      {
         "asset":"BTC",
         "borrowed":0,
         "free":224.65,
         "interest":0,
         "locked":0,
         "netAsset":224.65,
         "netAssetOfBtc":224.65
      },
      {
         "asset":"BNB",
         "borrowed":23.68,
         "free":256.02,
         "interest":0.0296,
         "locked":0,
         "netAsset":232.3104,
         "netAssetOfBtc":0.8068140192
      },
      {
         "asset":"USDT",
         "borrowed":0,
         "free":0,
         "interest":0,
         "locked":0,
         "netAsset":0,
         "netAssetOfBtc":0
      }
   ]
}
```


### Query margin asset (MARKET_DATA)

```
Get /sapi/v1/margin/query/asset 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
assete | STRING | YES |


**Response:**
```javascript
{
   "assetFullName":"binance-coin",
   "assetName":"BNB",
   "interestRate":0.1,
   "isBorrowable":true,
   "isMortgageable":true,
   "netAssetBorrowable":999900362.94725292
}
```

### Query margin pair (MARKET_DATA)
```
Get /sapi/v1/margin/query/pair 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |

**Response:**
```javascript
{
   "id":323355778339572400,
   "symbol":"BTCUSDT",
   "base":"BTC",
   "quote":"USDT",
   "isMarginTrade":true,
   "isBuyAllowed":true,
   "isSellAllowed":true
}
```

### Query margin priceIndex (MARKET_DATA)
```
Get /sapi/v1/margin/query/priceIndex 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

**Response:**
```javascript
{
   "calcTime":1554812564001,
   "price":0.0035344,
   "symbol":"BNBBTC"
}
```


### Query margin account's order (USER_DATA)

```
Get /sapi/v1/margin/query/order 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
orderId | STRING | NO |	
origClientOrderId | STRING | NO	|

Notes:

* Either orderId or origClientOrderId must be sent.
* For some historical orders cummulativeQuoteQty will be < 0, meaning the data is not available at this time.

**Response:**
```javascript
{
   "accountId":43720,
   "symbol":"BNBUSDT",
   "orderId":223465,
   "clientOrderId":"sadwsd3434R",
   "price":"0.20000000",
   "origQty":"0.34000000",
   "executedQty":"0.34000000",
   "cummulativeQuoteQty":"0.30600000",
   "status":"FILLED",
   "timeInForce":"GTC",
   "type":"LIMIT",
   "side":"SELL",
   "stopPrice":"0.00000000",
   "icebergQty":"0.00000000",
   "time":1555591172818,
   "updateTime":1555591172818,
   "isWorking":true
}
```

### Query margin account's open order (USER_DATA)
```
Get  /sapi/v1/margin/query/openOrders 
```

**Weight:**
10

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |


* If the symbol is not sent, orders for all symbols will be returned in an array.
* When all symbols are returned, the number of requests counted against the rate limiter is equal to the number of symbols currently trading on the exchange.

**Response:**
```javascript
{
   "symbol":"LTCBTC",
   "orderId":1,
   "clientOrderId":"myOrder1",
   "price":"0.1",
   "origQty":"1.0",
   "executedQty":"0.0",
   "cummulativeQuoteQty":"0.0",
   "status":"NEW",
   "timeInForce":"GTC",
   "type":"LIMIT",
   "side":"BUY",
   "stopPrice":"0.0",
   "icebergQty":"0.0",
   "time":1499827319559,
   "updateTime":1499827319559,
   "isWorking":true
}
```


### Query margin account's all order (USER_DATA)
```
Get /sapi/v1/margin/query/allOrders 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO	
startTime |	LONG | NO	
endTime | LONG | NO	
limit |	INT | NO | Default 500; max 1000.
Notes:

* If orderId is set, it will get orders >= that orderId. Otherwise most recent orders are returned.
* For some historical orders cummulativeQuoteQty will be < 0, meaning the data is not available at this time.

**Response:**
```javascript
{
   "symbol":"LTCBTC",
   "orderId":1,
   "clientOrderId":"myOrder1",
   "price":"0.1",
   "origQty":"1.0",
   "executedQty":"0.0",
   "cummulativeQuoteQty":"0.0",
   "status":"NEW",
   "timeInForce":"GTC",
   "type":"LIMIT",
   "side":"BUY",
   "stopPrice":"0.0",
   "icebergQty":"0.0",
   "time":1499827319559,
   "updateTime":1499827319559,
   "isWorking":true
}
```

### Query margin account's trade list (USER_DATA)
```
Get  /sapi/v1/margin/query/myTrades 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
startTime |	LONG | NO	
endTime | LONG | NO	
fromId | LONG | NO | TradeId to fetch from. Default gets most recent trades.
limit |	INT | NO | Default 500; max 1000.

Notes:


* If fromId is set, it will get orders >= that fromId. Otherwise most recent orders are returned.


**Response:**
```javascript
{
   "symbol":"BNBBTC",
   "id":28457,
   "orderId":100234,
   "price":"4.00000100",
   "qty":"12.00000000",
   "quoteQty":"48.000012",
   "commission":"10.10000000",
   "commissionAsset":"BNB",
   "time":1499865549590,
   "isBuyer":true,
   "isMaker":false,
   "isBestMatch":true
}
```

### Query max borrow (USER_DATA)
```
Get /sapi/v1/margin/maxBorrowable 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES |

**Response:**
```javascript
{"amount":  12321.11}
```

### Query max transfer-out amount (USER_DATA)
```
Get /sapi/v1/margin/maxTransferable 
```

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES |


**Response:**
```javascript
  {"amount":  12321.11}
```

### Start user data stream for margin account (USER_DATA)
```
POST  /sapi/v1/userDataStream
```

**Weight:**
1

**Parameters:**

NONE

**Response:**
```javascript
{"listenKey":  "T3ee22BIYuWqmvne0HNq2A2WsFlEtLhvWCtItw6ffhhdmjifQ2tRbuKkTHhr"}
```

### Delete user data stream for margin account  (USER_DATA)
```
DELETE  /sapi/v1/userDataStream
```

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES |

**Response:**
```javascript

```

## Ping user data stream for margin account  (USER_DATA)
```
PUT  /sapi/v1/userDataStream
```

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES |

**Response:**
```javascript

```
