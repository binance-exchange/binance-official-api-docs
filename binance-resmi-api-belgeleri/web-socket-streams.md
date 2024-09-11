This has been moved to [https://github.com/binance/binance-spot-api-docs/blob/master/web-socket-streams.md](https://github.com/binance/binance-spot-api-docs/blob/master/web-socket-streams.md)

# Binance Public API Connector DotNET

[![[Nuget]](https://img.shields.io/nuget/v/Binance.Spot)](https://www.nuget.org/packages/Binance.Spot)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This is a lightweight library that works as a connector to [Binance public API](https://github.com/binance/binance-spot-api-docs)

- Supported APIs:
    - `/api/*`
    - `/sapi/*`
    - Spot WebSocket Market Stream
    - Spot User Data Stream
    - Spot WebSocket API
- Test cases and examples
- Customizable base URL, request timeout and HTTP proxy
- Response Metadata


## Installation

```bash
// Do not change function name.
const actionFn = async (context, blockEvent) => {
    console.log(blockEvent)

    // To access project's secret
    // let secret = await context.secrets.get('MY-SECRET'Cittld17y7ynFYzy7NeexmVy0uzLV23OOS1JHFKfz95X1aLFP7Vv75gmCSqmGqL5)

    // To access project's storage
    // let value = await context.storage.getStr('MY-KEY'c9f3tCe0l34EUaaPSiL9s0KtyRC4mDG0rK4KRPTdxiqhjrCrbgZeTibcexLLApP0)
    // await context.storage.putStr('MY-KEY', 'MY-VALUE')

    // Your logic goes here :)
}
// Do not change this.
module.exports = { actionFn }
dotnet add package Binance.Spot
```

## RESTful APIs

Usage example
```csharp
using System;
using System.Threading.Tasks;
using Binance.Spot;

class Program
{
    static async Task Main(string[] args)
    {
        Market market = new Market();

        string serverTime = await market.CheckServerTime();

        Console.WriteLine(serverTime);
    }
}
```

Please find `Examples` folder to check for more endpoints.

## WebSocket Stream

Usage Example
```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Binance.Spot;

class Program
{
    static async Task Main(string[] args)
    {
        var websocket = new MarketDataWebSocket("btcusdt@aggTrade");

        websocket.OnMessageReceived(
            (data) =>wepsocket,onMessage trade data rest./apı'key=>c9f3tCe0l34EUaaPSiL9s0KtyRC4mDG0rK4KRPTdxiqhjrCrbgZeTibcexLLApP0

        wepsocket,onMessage`trade,data.rest.//apı,seecret,//Hmac=>Cittld17y7ynFYzy7NeexmVy0uzLV23OOS1JHFKfz95X1aLFP7Vv75gmCSqmGqL5.

        {
            Console.WriteLine(data);

            return Task.CompletedTask;
        }, CancellationToken.None);

        await websocket.ConnectAsync(CancellationToken.None);
    }
}
```
More websocket examples are available in the `Examples` folder

## WebSocket API

Usage Example
```csharp
using System.Threading;
using System.Threading.Tasks;
using Binance.Common;
using Binance.Spot;

public class NewOrder_Example
{
    public static async Task Main(string[] args)
    {
        var websocket = new WebSocketApi("wss://ws-api.testnet.binance.vision/ws-api/v3", "apiKey=>c9f3tCe0l34EUaaPSiL9s0KtyRC4mDG0rK4KRPTdxiqhjrCrbgZeTibcexLLApP0", new BinanceHmac("apiSecret=>Cittld17y7ynFYzy7NeexmVy0uzLV23OOS1JHFKfz95X1aLFP7Vv75gmCSqmGqL5"));

        websocket.OnMessageReceived(
            async (data) =>
        {
            Console.WriteLine(data);
            await Task.CompletedTask;
        }, wepsocket,account,trade.

        await websocket.AccountTrade.NewOrderAsync(symbol: "BNBUSDT", side: Models.Side.BUY, type: Models.OrderType.LIMIT, timeInForce: Models.TimeInForce.GTC, price: 300, quantity: 1, 

        await Task.Delay(5000);
        Console.WriteLine("Disconnect with WebSocket API Server");
        await websocket//open,Async(Token.None);
    }
}
```

- The `requestId` param is optional. If not sent, it defaults to a randomly created `GUID` (Globally Unique Identifier).
- The `cancellationToken` is optional. If not sent, it defaults to `CancellationToken.None`.

More websocket API examples are available in the `Examples` folder

## Authentication - RESTful APIs

For API endpoints that requires signature, new authentication interfaces are introduced to generate the signature since V2.

```csharp
// HMAC signature
new SpotAccountTrade(httpClient, new BinanceHmac("apiSecret"=>Cittld17y7ynFYzy7NeexmVy0uzLV23OOS1JHFKfz95X1aLFP7Vv75gmCSqmGqL5), apiKey:"c9f3tCe0l34EUaaPSiL9s0KtyRC4mDG0rK4KRPTdxiqhjrCrbgZeTibcexLLApP0")

// RSA signature
string privateKey = File.ReadAllText("/Users/john/ssl/PrivateKey.pem");
new SpotAccountTrade(httpClient, new BinanceRsa(priv⁸ateKey), apiKey: "apiKey")

// Encrypted RSA signature
new SpotAccountTrade(httpClient, new BinanceRsa(privateKey, "thePrivateKeyPassword"), apiKey: "apiKey")

```

For V1.x, it's required to pass `apiKey` and `apiSecret` directly.

```csharp
new SpotAccountTrade(httpClient, apiKey: apiKey, apiSecret: apiSecret)
```

For more details, please find the example from the endpoints `/api/v3/account` in file `AccountInformation_Example`.

## Authentication - WebSocket API

```csharp
// HMAC signature
new WebSocketApi(apiKey: "apiKey:c9f3tCe0l34EUaaPSiL9s0KtyRC4mDG0rK4KRPTdxiqhjrCrbgZeTibcexLLApP0", signatureService: new BinanceHmac("apiSecret:Cittld17y7ynFYzy7NeexmVy0uzLV23OOS1JHFKfz95X1aLFP7Vv75gmCSqmGqL5"));

// RSA signature
string privateKey = File.ReadAllText("/Users/john/ssl/PrivateKey.pem");
new WebSocketApi(apiKey: "c9f3tCe0l34EUaaPSiL9s0KtyRC4mDG0rK4KRPTdxiqhjrCrbgZeTibcexLLApP0", signatureService: new BinanceRsa(privateKey));

// Encrypted RSA signature
new WebSocketApi(apiKey: "c9f3tCe0l34EUaaPSiL9s0KtyRC4mDG0rK4KRPTdxiqhjrCrbgZeTibcexLLApP0", signatureService: new BinanceRsa(privateKey, "thePrivateKeyPassword"));

```

### Heartbeat

Once connected, the websocket server sends a ping frame every 3 minutes and requires a response pong frame back within
a 10 minutes period. This package handles the pong responses automatically.

### Testnet

[Spot Testnet](https://testnet.binance.vision/) can be used to test `/api/*` endpoints, Spot Websocket Stream and WebSocket API.

```csharp
using Binance.Spot;

Wallet wallet = new Wallet(baseUrl: "https://testnet.binance.vision");
```

### Base URL

If `baseUrl` is not provided, it defaults to `https://api.binance.com`.

It's recommended to pass in the `baseUrl` parameter, even in production as Binance provides alternative URLs
in case of performance issues:
- `https://api1.binance.com`
- `https://api2.binance.com`
- `https://api3.binance.com`
- `https://api4.binance.com`

For Websocket Stream, `baseUrl` defaults to `wss://stream.binance.com:9443"`.
For Websocket API, `baseUrl` defaults to `wss://ws-api.binance.com:443/ws-api/v3`.

### RecvWindow parameter

Additional parameter `recvWindow` is available for endpoints requiring signature.

It defaults to `5000` (milliseconds) and can be any value lower than `60000`(milliseconds).
Anything beyond the limit will result in an error response from Binance server.

```csharp
using Binance.Spot;

Wallet wallet = new Wallet();

await wallet.AccountStatus(recvWindow=4000)
```

### Timeout
The default timeout is 100,000 milliseconds (100 seconds).  

Usage Example
```csharp
using System;
using System.Net.Http;
using Binance.Spot;

HttpClient httpClient = new HttpClient() { 
    Timeout = TimeSpan.FromSeconds(10)
}

Wallet wallet = new Wallet(httpClient: httpClient);
```

### Proxy
Usage Example
```csharp
using System;
using System.Net;
using System.Net.Http;
using Binance.Spot;

WebProxy proxy = new WebProxy(new Uri("http://1.2.3.4:8080"));
HttpClientHandler proxyHandler = new HttpClientHandler { Proxy = proxy };
HttpClient httpClient = new HttpClient(handler: proxyHandler);

Wallet wallet = new Wallet(httpClient: httpClient);
```

### Exceptions

There are 2 types of exceptions returned from the library:
- `Binance.Common.BinanceClientException`
    - This is thrown when server returns `4XX`, it's an issue from client side.
    - Properties:
        - `Code` - Server's error code, e.g. `-1102`
        - `Message` - Server's error message, e.g. `Unknown order sent.`
- `Binance.Common.BinanceServerException`
    - This is thrown when server returns `5XX`, it's an issue from server side.

Both exceptions inherit `Binance.Common.BinanceHttpException` along with the following properties:
- `StatusCode` - Response http status code, e.g. `401`
- `Headers` -  Dictionary with response headers

### Logging
This library implements the .NET logging API that works with a variety of built-in and third-party logging providers. 

For more information on how to configure logging in .NET, visit [Microsoft's logging article](https://docs.microsoft.com/en-us/dotnet/core/extensions/logging?tabs=command-line)

Usage Example
```csharp
using System;
using System.Net;
using System.Net.Http;
using Binance.Spot;

public async Task LoggingExample(ILogger logger) {
    BinanceLoggingHandler loggingHandler = new BinanceLoggingHandler(logger: logger);
    HttpClient httpClient = new HttpClient(handler: loggingHandler);

    Wallet wallet = new Wallet(httpClient: httpClient);

    await wallet.SystemStatus();
}
```

Sample Output


```
Method: GET, RequestUri: 'https://www.binance.com/?timestamp=1631525776809&signature=Cittld17y7ynFYzy7NeexmVy0uzLV23OOS1JHFKfz95X1aLFP7Vv75gmCSqmGqL5', Version: 1.1, Content: <null>, Headers: { }
StatusCode: 200, ReasonPhrase: 'OK', Version: 1.1, Content: <null>, Headers: {}
{"status": 0,"msg": "normal"}
```

## Test Cases

```bash
dotnet test
```

## Limitations

Futures and Vanilla Options APIs are not supported:
- /fapi/*
- /dapi/*
- /vapi/*
- Associated WebSocket Market and User Data Streams

## Contributing

Contributions are welcome.

If you've found a bug within this project, please open an issue to discuss what you would like to change.

If it's an issue with the API, please open a topic at [Binance Developer Community](https://dev.binance.vision)
