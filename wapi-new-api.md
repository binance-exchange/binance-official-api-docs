# Public Rest API for Margin Trade (DRAFT, under construction)
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

# SIGNED (TRADE、USER_DATA AND MARGIN) Endpoint security
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
### Dust Transfer (USER_DATA)
```
Post /sapi/v1/asset/dust
```
Execute transfer from dust to BNB.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
dusts | ARRAY | YES | The asset being transferred. For example: microassets=BTC,microassets=USDT
recvWindow | LONG | NO |
timestamp | LONG | YES |


**Response:**
```javascript
{
    "totalServiceCharge":"0.02102542",
    "totalTransfered":"1.05127099",
    "transferResult":[
        {
            "amount":"0.03000000",
            "fromAsset":"ETH",
            "operateTime":1563368549307,
            "serviceChargeAmount":"0.00500000",
            "tranId":2970932918,
            "transferedAmount":"0.25000000"
        },
        {
            "amount":"0.09000000",
            "fromAsset":"LTC",
            "operateTime":1563368549404,
            "serviceChargeAmount":"0.01548000",
            "tranId":2970932918,
            "transferedAmount":"0.77400000"
        },
        {
            "amount":"248.61878453",
            "fromAsset":"TRX",
            "operateTime":1563368549489,
            "serviceChargeAmount":"0.00054542",
            "tranId":2970932918,
            "transferedAmount":"0.02727099"
        }
    ]
}
```

### Asset dividend record (USER_DATA)
```
Get /sapi/v1/asset/assetDividend
```
Query asset dividend record.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | NO |
startTime | LONG | NO |
endTime | LONG | NO |
recvWindow | LONG | NO |
timestamp | LONG | YES |

**Response:**
```javascript
{
    "rows":[
        {
            "amount":"10.00000000",
            "asset":"BHFT",
            "divTime":1563189166000,
            "enInfo":"BHFT distribution",
            "tranId":2968885920
        },
        {
            "amount":"10.00000000",
            "asset":"BHFT",
            "divTime":1563189165000,
            "enInfo":"BHFT distribution",
            "tranId":2968885920
        }
    ],
    "total":2
}
```
