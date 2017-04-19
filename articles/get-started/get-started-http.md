# HTTP API

HTTP で Milkcocoa を使う方法です。ライブラリのインストールは必要なく、HTTP GET を扱える環境であればどこでも利用することができます。

なお、 HTTP で Milkcocoa を使う場合、API Key による認証を行います。具体的には URL のパスに API Key を入れることで認証を行います。

About ページにはアプリ作成時に自動で生成される API Key が入った URL があらかじめ記載されています。


## データを Push（配信・保存）する

以下の URL に HTTP GET をリクエストすることで Push（データの配信・保存） ができます。

<a target="_blank" href="https://pubsub1.mlkcca.com/api/push/${appid}/${apikey}?c=demo/http&v={%22val%22:10}">https://pubsub1.mlkcca.com/api/push/${appid}/${apikey}?c=demo/http&v={"val":10}</a>

パラメータ `c` が DataStore 名、パラメータ `v` が配信・保存するデータ（JSON形式）になります。上述の例では `demo/http` というデータストアに、 `{"val":10}` というデータを配信・保存します。

試しに上述の URL をブラウザで開いてみてください。`{"err":null}` と表示されれば Push が成功しています。Dashboard の DataStore ページで `demo/http` を選択してデータが保存されているか確認できます。


## データを Subscribe（購読）する

以下の URL に HTTP GET をリクエストすることで Subscribe（データの購読） ができます。

<a target="_blank" href="https://pubsub1.mlkcca.com/on/push/${appid}/${apikey}?c=[[&quot;demo/http&quot;,0]]">https://pubsub1.mlkcca.com/on/push/${appid}/${apikey}?c=[["demo/http",0]]</a>

パラメータ `c` は二重の配列になります。子の配列の1番目に DataStore 名、2番目に購読するデータのタイムスタンプの最小値（※）が入ります。上述の例では `demo/http` というデータストアに購読します。

試しに上述の URL をブラウザで開いてみてください。レスポンスを待機している状態になります。この状態で先ほどの <a target="_blank" href="https://pubsub1.mlkcca.com/api/push/${appid}/${apikey}?c=demo/http&v={val:10}">Push の URL</a> を新しいタブなどで開いて Push してみると、待機している状態から、受信したデータ `{"demo/http":[[1234567890123456,"XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX","{\"val\":10}"]]}` といった表示に変わります。

※なお、購読するデータのタイムスタンプの最小値は、データを受信する度に、取得済みのデータのタイムスタンプよりも大きい値に更新する必要があります。Node.js の場合の具体的な実装例は以下になります。

```js
const request = require('request');

const DS = 'demo/http';
let ts = 0;
let options = {}

function subscribe() {
  options = {
    url: 'https://pubsub1.mlkcca.com/on/push/${appid}/${apikey}',
    method: 'GET',
    qs: {'c': JSON.stringify([[DS,ts]])}
  }

  request(options, function (error, response, body) {
    if (!error && response.statusCode == 200) {
      ts = JSON.parse(body)[DS][0][0];
      console.log(body);
      subscribe();
    }
  })
}

subscribe();
```


## データを取得する

以下の URL に HTTP GET をリクエストすることでデータの取得ができます。

<a target="_blank" href="https://pubsub1.mlkcca.com/api/history/${appid}/${apikey}?c=demo/http">https://pubsub1.mlkcca.com/api/history/${appid}/${apikey}?c=demo/http</a>

パラメータ `c` は DataStore 名になります。

試しに上述の URL をブラウザで開いてみてください。保存したデータが表示されます。

またパラメータ `ts` にタイムスタンプを指定することで、そのタイムスタンプより前のデータを取得することができます。以下の URL は開いてみてください。`ts` を `0` に設定しているため、1つもデータを取得できないことが確認できるかとおもいます。

<a target="_blank" href="https://pubsub1.mlkcca.com/api/history/${appid}/${apikey}?c=demo/http&ts=0">https://pubsub1.mlkcca.com/api/history/${appid}/${apikey}?c=demo/http&ts=0</a>


## データストアのリストを取得する

以下の URL に HTTP GET をリクエストすることでデータストアのリストを取得できます。

<a target="_blank" href="https://pubsub1.mlkcca.com/api/ds/${appid}/${apikey}?c=">https://pubsub1.mlkcca.com/api/ds/${appid}/${apikey}?c=</a>

パラメータ `c` にはデータストア名を入れて、前方一致でフィルターをかけることができます。以下の URL では `demo/` 以下のデータストアのリストを取得できます。

<a target="_blank" href="https://pubsub1.mlkcca.com/api/ds/${appid}/${apikey}?c=demo/">https://pubsub1.mlkcca.com/api/ds/${appid}/${apikey}?c=demo/</a>
