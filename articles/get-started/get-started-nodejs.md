# Node.js SDK

Node.js で Milkcocoa を使う方法です。ライブラリのインストールが必要です。

なお、 Node.js で Milkcocoa を使う場合、API Key による認証を行います。

## Milkcocoa のインストール

プロジェクトに [SDK](https://github.com/mlkcca/js) をインストールします。

```bash
$ npm install mlkcca/js
```

## Milkcocoa に接続する

`API_KEY` と `app_id` を使って Milkcocoa に接続します。

```js
const Milkcocoa = require('mlkcca');

const milkcocoa = new Milkcocoa({
  appId: '${appid}',
  apiKey: '${apikey}'
});
```

## データを Push（配信・保存）する

`DataStore` オブジェクトを利用して、データの Push を行います。

```js
milkcocoa.dataStore('demo/nodejs', {datatype: 'json'}).push({v:2});
```

`datatype` オプションでデータの型を指定できます。上記の例では JSON にしています。

## データを Subscribe（購読）する

`on()` を利用することで、Subscribe ができます。

```js
let ds = milkcocoa.dataStore('demo/nodejs', {datatype: 'json'});

ds.on('push', function (datum) {
  console.log('Pushed: '+datum.value.v);
});

ds.push({v:2});
```

実行すると、以下のようなログが出力されます。

```bash
# Log
Pushed: 2
```

データの形式は以下のようになっています。

```json
{
  "id" : "1e725aca-5613-1342-ae46-cdfe7f0db0a7",
  "timestamp" : 1492680909730,
  "value" : { "v" : 2 }
}
```

## データを取得する

`history()` を利用して、データの取得ができます。

```js
let ds = milkcocoa.dataStore('demo/nodejs', {datatype: 'json'});

ds.history({/* options */}, function(err, messages) {
  console.log(err, messages);
});
```

なお、第一引数にはオプションとして、データ数、昇順・降順、期間の指定ができます。

```js
// Default options
{
  limit: 100,
  order: 'desc', // or 'asc'
  ts: null // このタイムスタンプより前のデータを取得する
}
```
