# DataStore

DataStore とは、データベース兼メッセージブローカーです。

- データベース：クライアントから送信されたデータの保存・保存されたデータの取得
- メッセージブローカー：クライアント間のデータのやりとり

スラッシュ `/` で区切られた階層構造をもっていて、DataStore ごとにユーザーのアクセス権を割り当てることができます。

```json
/japan
/japan/tokyo
/japan/osaka
/usa
/usa/west/oregon
/usa/west/california
/usa/east/virginia
/usa/east/ohio
```

## データベースとしての DataStore

`push` もしくは `set` を使うことで DataStore にデータを保存できます。

スラッシュ `/` による階層構造にかかわらず、DataStore のパスごとに個別にデータが保存されます。例えば、以下のような構造になっていた場合を考えましょう。

```json
/japan
/japan/tokyo
/japan/osaka
```

3つのパスにそれぞれデータが保存されるため、`/japan/tokyo` に保存した場合、`/japan` にクエリをかけても `japan/tokyo` のデータには問い合わせることはありません。


## メッセージブローカーとしての DataStore

DataStore を指定して `on` を発行することで、該当する DataStore に対して発行される `push`, `set`, `send` を Subscribe 出来ます。

データベースのときとは違い、ワイルドカード `*` を使って前方一致が利用できます。

```json
/japan
/japan/tokyo
/japan/osaka
```

このような構造の場合、`/japan/tokyo` だけを Subscribe することも、`/japan/*` と `japan` 以下のすべてのパスを Subscribe することもできます。


## アクセスコントロール

DataStore に発行される API は DataStore ごとにアクセスコントロールできます。メッセージブローカーと同様、パスの単体指定に加え、ワイルドカード `*` を使って前方一致が利用できます。

### 利用例

例として、日本とアメリカに置いた IoT デバイスの監視を行うケースを考えてみます。

- 東京・大阪・オレゴン・カリフォルニア・バージニア・オハイオの6箇所にデバイスが置いてある
- デバイスは温度データを送信する
- 日本のみデータの保存も行う
- すべてのデバイスのデータを Web で閲覧できる

```json
/japan/tokyo
/japan/osaka
/usa
/usa/west
/usa/west/oregon
/usa/west/california
/usa/east
/usa/east/virginia
/usa/east/ohio
```

上記のような構造のとき、デバイスと Web の Rule は以下になります。

#### 東京にあるデバイス（大阪も同様）

```json
{
  "japan/tokyo" : ["write"]
}
```

#### オレゴンににあるデバイス（その他も同様）

```json
{
  "usa/west/oregon" : ["write"]
}
```

#### Web

```json
{
  "usa/*" : ["read"],
  "japan/*" : ["read"]
}
```

実案件では、Web からなにかしらの値を操作するために `usa/*` に `write` を許可したり、`usa/west` でオレゴンとカリフォルニアのデータを一旦集計する中継役のデバイスを置いて、`read` を許可したりする場合もあるかと思います。