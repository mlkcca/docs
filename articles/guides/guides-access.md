# アクセスコントロール

Milkcocoa は「**誰**が**どこ**に**何**をできる」をシンプルに設定できる、以下のようなアクセスコントロールシステムを持っています。

- 誰：1つの API Key もしくはアクセストークン
- どこ：DataStore のパス名
- 何：メソッド（`push`, `history`など）

アクセスコントロールは JSON 形式で記述します。

```json
{
  "DATASTORE_PATH1" : ["method1","method2",...],
  "DATASTORE_PATH2" : ["method1","method2",...],
  ...
}
```

例えば、以下のような指定をします。なお、ワイルドカード `*` を使うことで前方一致で指定できます。

```json
{
  "*" : ["history"],
  "japan/*" : ["push", "set", "send"]
}
```

## API Key のアクセスコントロールの設定方法

管理画面の *Access > API Key* で設定します。

![](/img/guides-access-api-key.png)

API Key を作成して、Rule にアクセスできる DataStore とメソッドを JSON 形式で記述します。


## アクセストークンのアクセスコントロールの設定方法

### Milkcocoa のアカウントを使って認証する場合

※こちらは開発用途になります。

Milkcocoa のアカウントで認証を行った場合、Milkcocao API を使用できるユーザーは以下の2パターンです。

- Milkcocoa アプリのオーナー
- Collaborator

Collaborator は管理画面の *Access > Collaborator* で設定できます。

![](/img/guides-access-token-collaborator.png)

### Twitter, Facebook, Github のアカウントを使って認証する場合

（不明）

### 自分でアクセストークンを発行する場合

`grant` API を `POST` メソッドで使用することで、アクセストークンのアクセスコントロールができます。

```bash
curl -H 'Content-Type:application/json' -d '{"rules":{"demo":["push"]}}' https://pubsub1.mlkcca.com/api/grant/${appid}/${apikey}
```