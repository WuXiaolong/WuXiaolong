title: Android studio share项目到svn仓库
date: 2015-05-25 
categories: [Android Studio]
tags: [Android Studio]
---

##import into SVN

![](http://wuxiaolong.qiniudn.com/2015-05-25-android-studio-share-to-svn-1.png)

##忽略文件

Android Studio 中建议过滤的文件：<br>
.idea 文件夹<br>
.gradle 文件夹<br>
所有的 build 文件夹<br>
所有的 .iml 文件<br>
local.properties 文件<br>

Android studio已经自动生成了git的忽略文件，如图：
<!-- more -->
![](http://wuxiaolong.qiniudn.com/2015-05-25-android-studio-share-to-svn-4.png)

但是SVN如下设置不知道为什么没有效果

![](http://wuxiaolong.qiniudn.com/2015-05-25-android-studio-share-to-svn-3.png)

目前有两种解决方案：

1、通过TortoiseSVN设置

![](http://wuxiaolong.qiniudn.com/2015-05-25-android-studio-share-to-svn-6.png)

2、上传代码忽略以上提及的文件

##update和commit

![](http://wuxiaolong.qiniudn.com/2015-05-25-android-studio-share-to-svn-5.png)

如果没有这两个按钮，通过下面的设置

![](http://wuxiaolong.qiniudn.com/2015-05-25-android-studio-share-to-svn-2.png)

附：

[【AndroidStudio】关于SVN的相关配置简介](http://blog.csdn.net/zhouzme/article/details/22790395)

[Android studio share项目到svn仓库](http://blog.csdn.net/hymking/article/details/45199077)



