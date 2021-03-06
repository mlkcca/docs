# 認証

Milkcocoa では、ユーザー ID を識別するために認証を行います。基本的に Milkcocoa の API を利用できるのは認証したユーザーのみです。ユーザー ID を識別することで、ユーザーが安全にアプリを利用できます。

Milkcocoa の API を使用する環境によって認証の方法が異なります。具体的には以下の2通りです。

- API Key 認証
- アクセストークン認証

## API Key 認証

最もシンプルな認証方法で、接続の際に API Key を送信することで認証ができます。

![](/img/guides-auth-api-key.svg)

API Key は公開することができないため、サーバーや IoT デバイスなど、主に外部からソースコードが見えない環境で使います。

### HTTP

`https://pubsub1.mlkcca.com/api/API_NAME/APP_ID/API_KEY` のように、URL のパスに API Key を入れることで認証を行います。

以下が、`push` を発行する場合です。

```bash
$ curl https://pubsub1.mlkcca.com/api/push/{{appid}}/{{apikey}}?c=demo/http\&v=10
```

### WebSocket

`wss://pubsub1.mlkcca.com/ws/API_NAME/APP_ID/API_KEY` のように、URL のパスに API Key を入れることで認証を行います。

以下が、`push` を発行する場合の URL です。

```bash
wss://pubsub1.mlkcca.com/ws/push/{{appid}}/{{apikey}}?c=demo/ws
```

### MQTT

MQTT の User/Pass 認証の部分に API Key を利用して認証を行います。`Username` に `API Key`（`Password` は `APP ID`）が入ります（先頭に `k` がつくことに注意してください）。

```bash
Username: k{{apikey}}
Password: {{appid}}
```

### Node.js

Milkcocoa に接続する際に、オプションとして API Key を渡します。

```js
const Milkcocoa = require('mlkcca');

const milkcocoa = new Milkcocoa({
  appId: '{{appid}}',
  apiKey: '{{apikey}}'
});
```

### Node-RED

`mlkcca` ノードの設定で、API Key を入力します。

- App ID : {{appid}}
- API Key : {{apikey}}

![](/img/get-started-node-red-settings.png)


## アクセストークン認証

Web や Mobile など、不特定多数が利用する場合の認証方法です。

### Milkcocoa が提供している ID Provider を利用する場合

ID Provider のアカウント情報をもとにアクセストークンを発行し、そのアクセストークンを使って認証を行います。

![](/img/guides-auth-access-token.svg)

現在は ID Provider は Milkcocoa のみです。今後、いくつかの著名なサービスでは簡単にアクセストークン認証を行うためのメソッドを用意する予定です。

具体的には、以下のような使い方をします。

```js
// Use Milkcocoa Account
Milkcocoa.authWithMilkcocoa({appId: '{{appid}}'}, function(err, milkcocoa) {
  // Use `milkcocoa` for any methods
});
```

実行をすると、ポップアップで Window が開いて それぞれのサービスのアカウントへの認証を求められます。認証が完了したら、第二引数のコールバック関数が呼ばれ、 `milkcocoa` オブジェクトを利用できます。

アクセスコントロールについては、<a href="{{accessPath}}">アクセスコントロールのドキュメント</a>をご覧ください。


### 自前でアクセストークンを発行する場合

上記に該当しないサービスや独自のアカウントシステムを利用する場合、API を使ってアクセストークンを発行する必要があります。

具体的には、ID Provider の認証情報を受け取るサーバーにて以下の API を発行します。

```bash
https://pubsub1.mlkcca.com/api/grant/{{appid}}/{{apikey}}
```

こちらの API を発行するとレスポンスとしてアクセストークンを返します。

```bash
# response
{
  "err" : null,
  "content" : {
    "access_token" : "XXXXXXXXXXXXXXXXXXXXXXXX", // tokenの中身
    "rules" : { // アクセス可能なデータストアリスト
      "*" : ["a"]
    },
    "ttl":864000 // データの保存期限
  }
}
```

`POST` メソッドを使用することで、上記 `rules` や `ttl` といった、トークンのアクセスコントロールができます。

```bash
curl -H 'Content-Type:application/json' -d '{"rules":{"demo":["write"]}}' https://pubsub1.mlkcca.com/api/grant/{{appid}}/{{apikey}}
```

アクセスコントロールについては、<a href="{{accessPath}}">アクセスコントロールのドキュメント</a>をご覧ください。