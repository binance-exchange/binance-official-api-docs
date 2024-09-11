## The Binance API documentation on Github has been moved to our official company repository:
[https://github.com/binance/binance-spot-api-docs](https://github.com/binance/binance-spot-api-docs)


# using System.Threading;
[_external_file_11352.PDF](https://github.com/user-attachments/files/16913445/_external_file_11352.PDF)
[Notes_240902_011853.txt](https://github.com/user-attachments/files/16913444/Notes_240902_011853.txt)
[Notes_240828_164046.PDF](https://github.com/user-attachments/files/16913443/Notes_240828_164046.PDF)


# using System.Threading.Tasks;
[Binance test net key.txt](https://github.com/user-attachments/files/16913438/Binance.test.net.key.txt)
[Binance api key.txt](https://github.com/user-attachments/files/16913437/Binance.api.key.txt)
[Yapilandirma dosyasi Notes_240816_010051.txt](https://github.com/user-attachments/files/16913436/Yapilandirma.dosyasi.Notes_240816_010051.txt)


# using Binance.Common;
[Binance api key.txt](https://github.com/user-attachments/files/16913448/Binance.api.key.txt)


# using Binance.Spot;
[Binance api key.txt](https://github.com/user-attachments/files/16913458/Binance.api.key.txt)
[adderall-prozac's Additions.csv](https://github.com/user-attachments/files/16913466/adderall-prozac.s.Additions.csv)
[export-0xdac17f958d2ee523a2206206994597c13d831ec7.csv](https://github.com/user-attachments/files/16913465/export-0xdac17f958d2ee523a2206206994597c13d831ec7.csv)
[README (1).md](https://github.com/user-attachments/files/16913464/README.1.md)
[rest-api.md](https://github.com/user-attachments/files/16913463/rest-api.md)


public class NewOrder_Example
{[coinstats_.date](https://github.com/user-attachments/files/16913509/coinstats_template.csv)

    public static async Task Main(string[] args)
    {
        var websocket = new WebSocketApi("wss://ws-api.testnet.binance.vision/ws-api/v3", "apiKey", new BinanceHmac("apiSecret"));

        websocket.OnMessageReceived(
            async (data) =>[Binance api key.txt](https://github.com/user-attachments/files/16913432/Binance.api.key.txt)
            

        {
            Console.WriteLine(data);
            await Task.CompletedTask;
        }, CancellationToken.None);

        await websocket.ConnectAsync(CancellationToken.None);

        await websocket.AccountTrade.NewOrderAsync(symbol: "BNBUSDT", side: Models.Side.BUY, type: Models.OrderType.LIMIT, timeInForce: Models.TimeInForce.GTC, price: 300, quantity: 1, : 
wepsocket.AccountTrade//data =>./[coinstats./Date](https://github.com/user-attachments/files/16913502/coinstats_template.csv)

        
    }
}
