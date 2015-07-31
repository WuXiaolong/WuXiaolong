title: 解决Android Studio和Android SDK Manager无法在线更新
date: 2015-01-21
categories: [Android]
tags: [Android,Android Studio]
---
##Android Studio

升级时提示 Connection failed. Please check your network connection and try again

修改安装目录下bin\studio.exe.vmoptions文件,如E:\Android\android-studio\bin\studio.exe.vmoptions

<!-- more -->

添加内容:

```js
-Djava.net.preferIPv4Stack=true
-Didea.updates.url=http://dl.google.com/android/studio/patches/updates.xml
-Didea.patches.url=http://dl.google.com/android/studio/patches/
```

重新启动..就可以在线更新了.

备注：如果是64位系统,那就应该修改bin\studio64.exe.vmoptions文件,大家别改错了,不然没用!

##Android SDK Manager

解决Android SDK Manager列表错误,或者无法更新下载的问题:

hosts文件添加内容:

C:\Windows\System32\drivers\etc

hosts文件windows系统目录中的\system32\drivers\etc下面，win98在windows系统目录中下面。

```js
203.208.46.146  www.google.com
74.125.113.121  developer.android.com
203.208.46.146 	dl.google.com
203.208.46.146 	dl-ssl.google.com

或者以下

#更新的内容从以下地址下载
74.125.113.121  developer.android.com
203.208.46.200 dl.google.com
203.208.46.200 dl-ssl.google.com
```
