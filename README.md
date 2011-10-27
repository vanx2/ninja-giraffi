オーバービュー
====================
node-ninja 上で、giraffi(logger)とMongoDBを使うためのサンプルです。

にんにん

giraffi
====================
packege.jsonにgiraffiを追加し

```
{
    "name": "application-name"
  , "version": "0.0.1"
  , "private": true
  , "dependencies": {
      "express": "2.4.4"
    , "jade": ">= 0.0.1"
    , "giraffi": ">= 0.0.1" // <= ココ
    , "mongoose": ">= 0.0.1"
  }
}
```

クライアントを作って

```js
giraffi_client = require('giraffi').createClient();
```

送信！

```js
giraffi_client.level("info").logger("some message");
```

レベルはある程度任意(Max 21文字)ですが、debug、info、error、fatal以外だとportalのログ画面から検索できません。
(最大1000行まで保存します。)

詳しい内容はこちらをどうぞ（英語）
[node-giraffi](https://github.com/giraffi/node-giraffi "node-giraffi")

Expressとの連携
-------------------
Expressのロガーとして指定できます。

```
app.use(giraffi.expressLogger(giraffi_client));
```

Expressのロガーのオプションもそのまま渡せます。

```
app.use(giraffi.expressLogger(giraffi_client, 'short'));
```

もし、アクセスログを取得したい場合は、app.use(app.router)より前でuseしてください。

```
app.use(giraffi.expressLogger(giraffi_client));
app.use(app.router);
```

また、エラーハンドラーとしても指定できます。
指定することにより、errorオブジェクトのstack traceをgiraffiに送信します。
本来のエラー処理を実行させるため、app.use(express.errorHandler())より前でuseしてください。

```
app.configure('production', function(){
  // set giraffi errorHandler
  app.use(giraffi.errorHandler(giraffi_client));
  app.use(express.errorHandler()); 
});
```

MongoDB
=============
Node NinjaではMachine起動時にデフォルトポートでMongoDBが起動されています。

mongoose
localhost のdb "test_ninja" に接続。

```
mongoose.connect('mongodb://localhost/test_ninja');
```

node-mongodb-native
localhostに接続。

```
var mongodb = require("mongodb");
var server = new mongodb.Server("127.0.0.1", 27017, {});
```
