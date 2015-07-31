title: Android如何防止apk程序被反编译
categories: [Android]
tags: [Android]
date: 2013-07-23
---
作为Android应用开发者，不得不面对一个尴尬的局面，就是自己辛辛苦苦开发的应用可以被别人很轻易的就反编译出来。Google似乎也发现了这个问题，从SDK2.3开始我们可以看到在android-sdk-windows\tools\下面多了一个proguard文件夹。proguard是一个java代码混淆的工具，通过proguard，别人即使反编译你的apk包，也只会看到一些让人很难看懂的代码，从而达到保护代码的作用。新建项目都会有以下两个文件：
![](http://wuxiaolong.qiniudn.com/2013-07-18-mobile-phone-information-acquisition-1.png)
project.properties文件“proguard.config=${sdk.dir}/tools/proguard/proguard-android.txt:proguard-project.txt”注释放开。
![](http://wuxiaolong.qiniudn.com/2013-07-18-mobile-phone-information-acquisition-2.png)
然后在proguard-project.txt文件里做混淆，例如：

<!-- more -->

```java
	#---------------Begin: support-v4包 ----------
	-libraryjars   libs/android-support-v4.jar
	-dontwarn android.support.v4.**  #打包时忽略以下类的警告  
	-keep class android.support.v4.** { *; }  
	-keep interface android.support.v4.app.** { *; }  
	-keep public class * extends android.support.v4.**  
	#-keep public class * extends android.app.Fragment
	#---------------End: support-v4包 ----------
```
以上是混淆了support-v4包，如果混淆其他的jar，举一反三。
比如A.jar，它的包名是a.b.c.d。然后做混淆就如下：


```java 
	-keep class a.b.c.d.** { *; }  
```
正常的编译签名后就可以防止代码被反编译了。反编译经过代码混淆的apk得到的代码应该类似于下面的效果，是很难看懂的：
![](http://wuxiaolong.qiniudn.com/2013-07-18-mobile-phone-information-acquisition-3.png)
