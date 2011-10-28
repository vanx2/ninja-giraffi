Overview
====================
node-ninja 上で、giraffi(logger)とMongoDBを使うためのサンプルです。

にんにん

giraffi
====================
ログサービスです。最大1000行、もしくは5Mまで保存します。

Clientのnpmの情報はこちら=>
[https://github.com/giraffi/node-giraffi](https://github.com/giraffi/node-giraffi "node-giraffi")

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

console.logの出力内容をなどは送信していませんので、実際にMachineにログインして下記のログを参照してください。

```
tail -f /var/svc/log/site-node-service:default.log
```


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

エラーハンドラーとして指定することにより、errorオブジェクトのstack traceをgiraffiに送信します。

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

AND...
===================
もし、インストールしたアプリケーションを改修するなら。

git cloneして (node@ の後ろのipはOrderしたMachineのip)

```
~/product/node-ninja $ git clone node@0.0.0.0:repo
Cloning into repo...
remote: Counting objects: 52, done.        
remote: Compressing objects: 100% (22/22), done.        
Receiving objects: 100% (52/52), 6.58 KiB, done.
remote: Total 52 (delta 24), reused 52 (delta 24)        
Resolving deltas: 100% (24/24), done.
```

edit!して

```
~/product/node-ninja $ cd repo
~/product/node-ninja/repo $ git diff
diff --git a/server.js b/server.js
index 5f2b9f0..acb504d 100644
--- a/server.js
+++ b/server.js
@@ -54,7 +54,7 @@ app.configure('production', function(){
 // Routes
 app.get('/', function(req, res){
   res.render('index', {
-    title: 'Express'
+    title: 'Express Karano-'
   });
 });
```

commit!して

```
~/product/node-ninja/repo $ git commit -am "karano-"
[master eabe854] karano-
 1 files changed, 1 insertions(+), 1 deletions(-)
```

push!する

```
~/product/node-ninja/repo $ git push
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 297 bytes, done.
Total 3 (delta 2), reused 0 (delta 0)

~~~~~略

remote: Starting node v0.4.12...        
remote: Successful        
To node@0.0.0.0:repo
   adc89be..eabe854  master -> master
```

以上！

ブラウザで確認すれば変更されているはずです。
