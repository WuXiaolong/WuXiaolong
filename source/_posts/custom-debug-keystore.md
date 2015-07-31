title: Eclipse ADT的Custom debug keystore所需证书规格
date: 2014-06-30 
categories: [Android]
tags: [Android]
---
自定义的调试证书，也需要保证和默认证书一样的密码，别名alias和别名密码。

默认证书信息如下：

```js
Keystore name: “debug.keystore”
Keystore password: “android”
Key alias: “androiddebugkey”
Key password: “android”
```

遵循这个规则就可以用自定义调试证书了。

Android apk签名方法
http://hi.baidu.com/40406_jun/item/8b936db4a9a0e79b19469782
