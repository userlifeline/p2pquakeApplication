# <span style="color:#9DCCE0;">P2PMidpoint 仕様書</span><span style="font-size: 60%;"><div style="text-align: right;">Developed by userlifeline</div></span>

## 仕組み

P2PMidpoint(以下、本アプリ)はTCPサーバーを公開することでデータを同PCのアプリ内で共有することができます.

## 基本仕様

URI: http://localhost:12345 (ポート番号はアプリ側で指定することができます(``12345``はデフォルト指定です).)  
リクエストメゾット: GET

## 詳細

本アプリは起動時にTCPサーバーを起動し、接続要求が来る時にHTTPレスポンスを返却することで接続処理を行っています.  
本アプリが起動している間は``while``文で常時接続を許可しています.

以下はサンプルです.

```http
> GET http://localhost:12345 HTTP/1.0

< HTTP/1.0 200 OK
< CONTENT-TYPE: APPLICATION/JSON; CHARSET=UTF-8
```

レスポンスコードは常に``200``です.  
返答されるボディについてはP2P地震情報 JSON API のデータです.
[詳細](https://github.com/p2pquake/epsp-specifications/blob/master/json-api-v1.md)

## 接続ソース例

以下にC#の場合の接続例を示します.

```cs
public const int midpointPORT = 12345;

public async Task<string> GetInformationAsync()
{
    HttpClient client = new HttpClient();
    var responceMessage = await client.GetAsync($"http://localhost:{midpointPORT}");
    responceMessage.EnsureSuccessStatusCode();
    var responceBody = await responceMessage.Content.ReadAsStringAsync();
    return responceBody;
}
```

これまでのJSON APIの取得部分をこのように書き換えることで取得することができます.  

また、関数内で取得する場合については以下に示します.

```cs
using Systen.Diagnostics;

public HttpClient client = new HttpClient();

public bool getFromP2PM = true; //P2PMidpointから取得するか
public string json = string.Empty;
public const int P2PMidpointPORT = 12345;

public void Get()
{
    Task.Run(new Action(() => {
        try
        {
            Console.Write("サーバーにリクエストを送信しています");

            if(getFronP2PM)
            {
                Process[] processes = Process.GetProcessesByName("P2PMidpoint");
                if (proccesses.Length == 0)
                {
                   Console.WriteLine("\r\nP2PMidpointが見つかりませんでした. 接続をJSON APIからの直接接続に変更します.");
                    getFronP2PM = false;

                    json = await client.GetStringAsync("http://api.p2pquake.net/v1/human-readable");
                }
                else
                {
                    var responseMessage = await client.GetAsync($"http://localhost:{P2PMidpointPORT}");
                    responseMessage.EnsureSuccessStatusCode();
                    var responceBody = await responseMessage.Content.ReadAsStringAsync();
                    json = responceBody;
                }
            } else json = await client.GetStringAsync("http://api.p2pquake.net/v1/human-readable");
        }
        catch (Exception exception)
        {
            Console.WriteLine($"\r\n接続に失敗しました; Exception={exception.Message}");
        }
    }));
}

```

この例では従来のJSON APIへの直接接続と本アプリへの接続を自動的に変更します.

## THANKS

### 情報提供

- 情報取得元
  > 気象庁
- P2P地震情報開発者
  > たくや様 [Twitter](https://twitter.com/p2pquake_takuya)
- デバッガー
  > デバッガーサーバー「Lifeline's App」に参加されている方々

## 連絡先

いずれかから連絡してください. 件名を書いていただけると幸いです.

- Twitter : [@userlifeline](https://twitter.com/userlifeline)
- Discord : ``生命線#7696``
- デバッグサーバー(Discord) : [参加する](https://discord.gg/EEvB7YquPx)

> このアプリが皆さまの地震監視・観測に利用していただけると願って.  
Copyright 2021 userlifeline, ALL RIGHTS RESERVED.
