# WebSocket API

WebSocket で Milkcocoa を使う方法です。ライブラリのインストールは必要はありません。

なお、 WebSocket で Milkcocoa を使う場合、API Key による認証を行います。具体的には URL のパスに API Key を入れることで認証を行います。

About ページにはアプリ作成時に自動で生成される API Key が入った URL があらかじめ記載されています。


## Milkcocoa に接続する

Node.js(※) を例にとると、Milkcocoa に接続するコードを以下になります。

```js
const WebSocketClient = require('websocket').client;

const client = new WebSocketClient();

client.on('connectFailed', function(error) {
  console.log('Connect Error: ' + error.toString());
});

client.on('connect', function(connection) {
  console.log('WebSocket Client Connected');
  connection.on('error', function(error) {
    console.log("Connection Error: " + error.toString());
  });
  connection.on('close', function() {
    console.log('echo-protocol Connection Closed');
  });
});

client.connect('wss://pubsub1.mlkcca.com/ws/push/{{appid}}/{{apikey}}?c=demo/ws');
```

`client.connect` の URI 内のパラメータ `c` が DataStore 名になります。上述の例では `demo/ws` というデータストアに購読します。

※ API Key は公開することができないため、ブラウザで利用する場合は API Key ではなくアクセストークンを利用します。

## データの Subscribe（購読）と Push（配信・保存）

以下のコードは、`demo/ws` というデータストアを Subscribe して、`demo/ws` に 1000ms ごとにデータを Push する例です。

```js
const WebSocketClient = require('websocket').client;

const client = new WebSocketClient();

client.on('connectFailed', function(error) {
  console.log('Connect Error: ' + error.toString());
});

client.on('connect', function(connection) {
  console.log('WebSocket Client Connected');
  connection.on('error', function(error) {
    console.log("Connection Error: " + error.toString());
  });
  connection.on('close', function() {
    console.log('echo-protocol Connection Closed');
  });
  connection.on('message', function(message) {
    if (message.type === 'utf8') {
      let received = {
        timestamp: JSON.parse(message.utf8Data)[0][0],
        id: JSON.parse(message.utf8Data)[0][1],
        payload: JSON.parse(message.utf8Data)[0][2],
      };
      console.log(JSON.stringify(received));
    }
  });

  function sendNumber() {
    if (connection.connected) {
      let number = Math.round(Math.random() * 0xFFFFFF);
      connection.sendUTF('ws'+number.toString());
      setTimeout(sendNumber, 1000);
    }
  }
  sendNumber();
});

client.connect('wss://pubsub1.mlkcca.com/ws/push/{{appid}}/{{apikey}}?c=demo/ws');
```

send API を使いたい場合は、 URL の `push` の部分を `send` に変えます。