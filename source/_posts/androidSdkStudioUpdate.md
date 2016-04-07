title: 【译】Android SDK、Android Studio在线更新
date: 2016-04-10 20:23:25
tags: [SDK,Android Studio]
category: Android
---
> 重点介绍网站http://www.androiddevtools.cn/，收集整理Android开发所需的Android SDK、开发中用到的工具、Android开发教程、Android设计规范，免费的设计素材等。感谢脉脉不得语，分享这么棒的AndroidTools！


# 前言
> 原文地址:[http://android-mirror.bugly.qq.com:8080/include/usage.html](http://android-mirror.bugly.qq.com:8080/include/usage.html)

`新手必知技能`：由于墙内，您有么有为无法更新SDk以及Android Studio无法更新版本而烦恼呢，这里分享一篇教程帮您解决此烦恼，原文是英文，我适当做了翻译（我本身英文是渣渣，基本是意译）。

# Android SDK在线更新镜像服务器

1、 打开 Android SDK Manager  
![](http://7q5c2h.com1.z0.glb.clouddn.com/sdk_manager.png)
2、配置用户自定义网站
点击 "User Defined Sites"，新建，添加http://android-mirror.bugly.qq.com:8080/android/repository/addon.xml  
![](http://7q5c2h.com1.z0.glb.clouddn.com/site.png)
3、配置端口
勾上 Force https://... sources to be fetched using http://... 
 ![](http://7q5c2h.com1.z0.glb.clouddn.com/proxy.png)

笔者：重新打开Android SDK Manager，发现居然能在线更新 

# Android Studio在线更新

1、打开Android Studio安装的bin目录，如'D:\Android Studio\bin' 
![](http://7q5c2h.com1.z0.glb.clouddn.com/asd1.png)

2、用文本编辑器（Notepad++），64位，打开 'studio64.exe.vmoptions'，32位，则打开studio.exe.vmoptions' 
![](http://7q5c2h.com1.z0.glb.clouddn.com/asd2.png)

3、在studio64.exe.vmoptions文件添加如下：
-Djava.net.preferIPv4Stack=true
-Didea.updates.url=http://android-mirror.bugly.qq.com:8080/AndroidStudio/patches/updates.xml
-Didea.patches.url=http://android-mirror.bugly.qq.com:8080/AndroidStudio/patches/ 

4、点击检测升级Android Studio
![](http://7q5c2h.com1.z0.glb.clouddn.com/asd3.png)

点击 'Update and Restart'. 
![](http://7q5c2h.com1.z0.glb.clouddn.com/asd4.png)
![](http://7q5c2h.com1.z0.glb.clouddn.com/incremental.png)
![](http://7q5c2h.com1.z0.glb.clouddn.com/restart.png)

笔者：到此，Android Studio更新版本完成。

# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_AndroidProgrammer.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

