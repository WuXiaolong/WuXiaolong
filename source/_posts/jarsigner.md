title: jarsigner签名安卓Apk程序
date: 2014-12-05 
categories: [Android]
tags: [Android,jarsigner]
---

发布过Android应用的朋友们应该都知道，Android APK的发布是必需要签名，本签名指南演示如何使用WoSign安卓代码签名证书，签名安卓 .apk 程序。

1、签名证书

制作证书，证书文件名：android.keystore, 证书别名：android.keystore
 
2、 安装签名环境JDK包

签名工具包可以在JDK官方网站下载：[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)，也可以直接点击这里下载JDK工具包(请使用JDK 1.6版本)。
文件包下载安装完成后，然后就开始进入jdk的签名根目录，具体如下：

开始-> 运行-> cmd->cd 到您安装的jdk的目录这里我是D:\Program Files\Java\jdk1.6.0\bin
 
<!-- more -->
3、签名

在D:\Program Files\Java\jdk1.6.0\bin 找到工具 jarsigner.exe，并把证书android.keystore 文件放入当前目录。签名命令：

jarsigner -verbose -keystore android.keystore -signedjar Last_gongs_sign.apk Before_sign.apk android.keystore

1）jarsigner是工具名称，-verbose表示将签名过程中的详细信息打印出来，显示在dos窗口中；

2）-keystore android.keystore 表示签名所使用的数字证书和所在位置，这里没有写路径，表示在当前目录下；

3）-signedjar Last_gongs_sign.apk Before_sign.apk 表示给 Before_sign.apk文件签名，签名后的文件名称为Last_gongs_sign.apk；

4）最后面的android.keystore 表示证书的别名
 
如下图所示：

![](http://wuxiaolong.qiniudn.com/2014-12-05-jarsigner-1.png)

 
4、验证签名

使用命令验证已经签名的apk文件：jarsigner –verify Last_gongs_sign.apk

![](http://wuxiaolong.qiniudn.com/2014-12-05-jarsigner-2.png)

如果想要知道更多明细, 你可以尝试如下几个命令:

    jarsigner -verify -verbose Last_gongs_sign.apk                                            
或
    jarsigner -verify -verbose -certs Last_gongs_sign.apk  
    
上面的命令, 使用 -certs 参数, 将显示 "CN=" 行, 描述是谁签名了APK文件.

原文：[http://feelyou.info/trying_to_manually_sign_android_package_with_jarsigner_but_install_failed/](http://feelyou.info/trying_to_manually_sign_android_package_with_jarsigner_but_install_failed/)
