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

送信！
```js
giraffi_client = require('giraffi').createClient();
giraffi_client.level("info").logger("some message");
```
