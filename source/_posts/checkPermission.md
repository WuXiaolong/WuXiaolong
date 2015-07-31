title: Android判断权限被限制
date: 2015-06-10 
categories: [Android]
tags: [Android,Permission]
---

开发过程，如果某个权限被第三方限制了，比如录音，网上查了下

```java
 String permName = "android.permission.RECORD_AUDIO";
        String pkgName = getPackageName();
        // 结果为0则表示使用了该权限，-1则表求没有使用该权限
        int reslut = getPackageManager().checkPermission(permName, pkgName);
        Log.d("wxl", "reslut=" + reslut + "======"
                + PackageManager.PERMISSION_GRANTED);
```
实际这个方法只是检测你的APP是否使用了某个权限，但是不能检测是否被限制了。

<!-- more -->

我以为做不了，找了微信，限制录音权限，结果提示录音权限被限，于是我仔细看了异常信息。

```js
06-11 11:33:58.644    7706-7706/cn.cntv.tvt W/System.err﹕ java.io.IOException: Recording permission has been disabled for current app
06-11 11:33:58.644    7706-7706/cn.cntv.tvt W/System.err﹕ at com.lbe.security.service.core.client.MultimediaClient.mrShouldBlock(Unknown Source)
06-11 11:33:58.644    7706-7706/cn.cntv.tvt W/System.err﹕ at android.media.MediaRecorder._setOutputFile(Native Method)
06-11 11:33:58.644    7706-7706/cn.cntv.tvt W/System.err﹕ at android.media.MediaRecorder.prepare(MediaRecorder.java:769)
```
对比Recording permission has been disabled for current app，做相应的提示就OK！

