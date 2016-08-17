title: 【译】Android Studio、Android SDK在线更新
date: 2016-04-08 20:23:25
tags: [SDK,Android Studio]
category: Android
---
> 重点介绍网站http://www.androiddevtools.cn/，收集整理Android开发所需的Android SDK、开发中用到的工具、Android开发教程、Android设计规范，免费的设计素材等。感谢脉脉不得语，分享这么棒的AndroidTools！

# 号外
![](http://7q5c2h.com1.z0.glb.clouddn.com/as2.0.png)
就在今天，Android Studio终于迎来了2.0正式版更新（此版本最重要的两个更新是：即时运行和 GPU Profiler。同时还有全新的 Android 开发套件，速度强行提升 50 倍），之前Android Studio Preview和Beta版本还存在很多bug，因此不建议更新。

升级注意问题
1、点击Check for Update不能出现上面的`增量升级`提示，多点击几次Check for Update
2、升级完成，默认配置gradle1.5，进入后会报错，会有提示，做相应gradle同步
<!--more-->
# 前言
> 原文地址:[http://android-mirror.bugly.qq.com:8080/include/usage.html](http://android-mirror.bugly.qq.com:8080/include/usage.html)

`新手必知技能`：您去更新升级Android Studio时却提示 Connection failed. Please check your network connection and try again，shit，由于墙内，您也无法更新Android SDK，这里分享一篇教程帮您解决此烦恼，原文是英文，我适当做了翻译（本身我英文是渣渣，基本是意译）。

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

# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

