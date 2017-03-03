HTTP API
=====

## データを扱う

### push data

```
https://pubsub1.mlkcca.com/api/push/${appid}/${apikey}?c=demo&v={val:10}
```

[push test](https://pubsub1.mlkcca.com/api/push/demo/demo?c=topic&v={val:10})


### 最近のデータを取得する

```
https://pubsub1.mlkcca.com/api/history/${appid}/${apikey}?c=demo
```

[history test](https://pubsub1.mlkcca.com/api/history/demo/demo?c=topic)


### 過去データを取得する

```
https://pubsub1.mlkcca.com/api/history/${appid}/${apikey}?c=demo&ts=1488531400000
```

[history test](https://pubsub1.mlkcca.com/api/history/demo/demo?c=topic&ts=1488531400000)


### データストアのリストを取得する

```
https://pubsub1.mlkcca.com/api/ds/${appid}/${apikey}?c=/
```

[datastore list test](https://pubsub1.mlkcca.com/api/ds/demo/demo?c=/)


## API Key and AccessToken

### Grant

```
https://pubsub1.mlkcca.com/api/grant/${appid}/${apikey}?rules=["*":["read"]]
```

### Access with access token

use Authorization header.

```
curl -H 'Authorization: bearer ${accessToken}' -X GET https://pubsub1.mlkcca.com/api/history/${appid}?c=demo
```



