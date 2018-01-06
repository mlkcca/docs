# アクセスコントロール

Milkcocoa は「**誰**が**どこ**に**何**をできる」をシンプルに設定できる、以下のようなアクセスコントロールシステムを持っています。

- 誰：1つの API Key もしくはアクセストークン
- どこ：DataStore のパス名
- 何：権限（`write`, `read`, `manage`, `any`）

アクセスコントロールは JSON 形式で記述します。

```json
{
  "DATASTORE_PATH1" : ["permission1","permission2",...],
  "DATASTORE_PATH2" : ["permission1","permission2",...],
  ...
}
```

権限の詳細は以下のようになっています。

|  | grant | push | set | send | history | get | ds | on |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **any** | ✔︎ | ✔︎ | ✔︎ | ✔︎ | ✔︎ | ✔︎ | ✔︎ | ✔︎ |
| **manage** | ✔︎ |   |   |   |   |   |   |   |
| **write** |   | ✔︎ | ✔︎ | ✔︎ |   |   |   |   |
| **read** |   |   |   |   | ✔︎ | ✔︎ | ✔︎ | ✔︎ |

なお、`manage` は `*` にのみ指定できます（その他のデータストアパスに指定することはできません）。

具体的には、以下のような指定をします。なお、ワイルドカード `*` を使うことで前方一致で指定できます。

```json
{
  "*" : ["read"],
  "japan/*" : ["write"]
}
```

データストアパスに複数のルールが適応された場合は、**両方とも適応**されます。上記のようなルールであれば、例えばデータストア `japan`, `japan/tokyo`, `usa` のアクセス権は以下のようになります。

|   | japan | japan/tokyo | usa |
| --- | --- | --- | --- |
| * | read︎ | read︎ | read︎ |
| japan/* | write | write |   |
|   | **read/write** | **read/write** | **read** |

## API Key のアクセスコントロールの設定方法

管理画面の *Access > API Key* で設定します。

![](https://i.gyazo.com/a3ccedd0117bc7c0e0647e7a38e6854a.png)

API Key を作成して、Rule にアクセスできる DataStore とメソッドを JSON 形式で記述します。


## アクセストークンのアクセスコントロールの設定方法

### Milkcocoa のアカウントを使って認証する場合

※こちらは開発用途になります。

Milkcocoa のアカウントで認証を行った場合、Milkcocao API を使用できるユーザーは以下の2パターンです。

- Milkcocoa アプリのオーナー
- Collaborator

Collaborator は管理画面の *Access > Collaborator* で設定できます。

![](/img/guides-access-token-collaborator.png)


### 自分でアクセストークンを発行する場合

`grant` API を `POST` メソッドで使用することで、アクセストークンのアクセスコントロールができます。

```bash
curl -H 'Content-Type:application/json' -d '{"rules":{"demo":["write"]}}' https://pubsub1.mlkcca.com/api/grant/{{appid}}/{{apikey}}
```