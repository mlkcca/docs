# MQTT API

MQTT で Milkcocoa を使う方法です。ライブラリのインストールは必要はありません。

なお、 MQTT で Milkcocoa を使う場合、API Key による認証を行います。具体的には Username に API Key を入れることで認証を行います。

About ページにはアプリ作成時に自動で生成される API Key が入った 接続情報が記載されています。


## Milkcocoa に接続する

Node.js(※) を例にとると、Milkcocoa に接続するコードを以下になります。

```js
const mqtt = require('mqtt')

const MQTT_URL = 'mqtt://pubsub1.mlkcca.com:1883'
const TOPIC = '${appid}/demo/mqtt'
const options = {
  username: '${apikey}',
  password: '${appid}'
}
const client  = mqtt.connect(MQTT_URL, options)

client.on('connect', function () {
  console.log('connected')
})
```

`TOPIC` の `appid/` 以下が DataStore 名になります。上述の例では `demo/mqtt` というデータストアに接続します。

## データの Push（配信・保存）

以下のコードは、`demo/mqtt` にデータを Push する例です。

```js
const mqtt = require('mqtt')

const MQTT_URL = 'mqtt://pubsub1.mlkcca.com:1883'
const TOPIC = '${appid}/demo/mqtt'
const options = {
  username: '${apikey}',
  password: '${appid}'
}
const client  = mqtt.connect(MQTT_URL, options)

client.on('connect', function () {
  console.log('connected')
  client.publish(TOPIC, 'Hello mqtt!')
  console.log('Pushed: Hello mqtt!')
  client.end()
})
```

Dashboard の DataStore ページで `demo/mqtt` を選択してデータが保存されているか確認できます。