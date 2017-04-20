# Node-RED

Node-RED で Milkcocoa を使う方法です。

なお、Node-RED で Milkcocoa を使う場合、API Key による認証を行います。具体的には、Node の設定に API Key を登録します。


## 事前準備

ローカル環境で使う方法を見ていきましょう。

プロジェクトに移動して Node-RED をインストールしましょう。

```bash
$ npm init
$ npm install node-red
```

インストールが終わったら、`package.json` の `scripts` に `"start": "node-red"` を追加します。

```json
{
  ...
  "scripts": {
    "start": "node-red",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  ...
}

```

## Milkcocoa のインストール

プロジェクトに [mlkcca ノード](https://github.com/mlkcca/node-red-contrib-mlkcca) をインストールします。

```bash
$ npm install mlkcca/node-red-contrib-milkcocoa
```

## Node-RED を立ち上げる

以下のコマンドで Node-RED を立ち上げます。

```bash
$ npm start
```

ログに `[info] Server now running at http://127.0.0.1:1880/` と表示されるので、ブラウザで `http://127.0.0.1:1880/` を開くと Node-RED のエディタ画面が表示されます。


## Milkcocoa Node の設定

サイドバーの「出力」から `mlkcca` ノードをシートに持ってきます。

![](/img/get-started-node-red-add-out.png)

Node をダブルクリックすると設定画面が出てくるので、鉛筆マークから Milkcocoa の `app_id` と `API_KEY` を入力します。それぞれの情報は以下です。

- `app_id` : ${appid}
- `API_KEY` : ${apikey}

![](/img/get-started-node-red-settings.png)

DataStore は `demo/node-red` にしておきましょう。これで Milkcocoa Node の設定は完了です。

![](/img/get-started-node-red-ds-out.png)


## Node-RED から Milkcocoa にデータを Push（配信・保存）する

`inject` ノードを使って、Node-RED から Milkcocoa にデータを保存してみましょう。

サイドバーから `inject` ノードをシートに持ってきて、`mlkcca` ノードにつなげます。

![](/img/get-started-node-red-inject.png)

右上の「デプロイ」ボタンでデプロイします。

![](/img/get-started-node-red-inject-deploy.png)

`inject` ノードの左にあるインジェクトボタンを押すと、Push されます。

![](/img/get-started-node-red-inject-trigger.png)

Dashboard の DataStore ページで `demo/node-red` を選択してデータが保存されているか確認できます。


## Milkcocoa に Push されるデータを Node-RED 側で Subscribe（購読） する

`debug` ノードを使って、Milkcocoa へ Subscribe してデータの監視をしてみましょう。

今度はサイドバーの「入力」から `mlkcca` ノードをシートに持ってきます。

![](/img/get-started-node-red-add-in.png)

`mlkcca` ノードの設定画面を開き、DataStore を `demo/node-red` にします。なお、`app_id` や `API_KEY` の設定は Push で既に行っているので不要です。

![](/img/get-started-node-red-ds-in.png)

サイドバーから `debug` ノードをシートに持ってきて、`mlkcca` ノードにつなげます。

![](/img/get-started-node-red-debug.png)

右上の「デプロイ」ボタンでデプロイします。

![](/img/get-started-node-red-debug-deploy.png)

ここで `demo/node-red` にデータを Push してみましょう。以下の URL をブラウザで開いて Push します。

<a target="_blank" href="https://pubsub1.mlkcca.com/api/push/${appid}/${apikey}?c=demo/node-red&v=123">https://pubsub1.mlkcca.com/api/push/${appid}/${apikey}?c=demo/node-red&v=123</a>

Pushをしたら Node-RED エディタの debug の部分にデータが表示されます。

![](/img/get-started-node-red-debug-view.png)