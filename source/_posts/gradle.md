title: Android build.gradle文件
date: 2015-11-10 15:03:23
tags: gradle
category: Android
---
app/build.gradle

Android Studio使用Gradle 编译运行Android工程，工程的每个模块以及整个工程都有一个build.gradle文件。通常你只需要关注模块的build.gradle文件，该文件存放编译依赖设置，包括defaultConfig设置：
<!--more-->
## compiledSdkVersion 
应用将要编译的目标Android版本，此处默认为你的SDK已安装的最新Android版本，我们仍然可以使用较老的版本编译项目，但把该值设为最新版本，可以使用Android的最新特性，同时可以在最新的设备上优化应用来提高用户体验。

## buildToolsVersion
使用什么版本的编译工具。使用SDK Manager安装多个版本的编译工具。

## applicationId 
创建新项目时指定的包名。

## minSdkVersion 

创建项目时指定的最低SDK版本，是新建应用支持的最低SDK版本。

## targetSdkVersion 
表示你测试过你的应用支持的最高Android版本(同样用API level表示)，当Android发布最新版本后，我们应该在最新版本的Android测试自己的应用同时更新target sdk到Android最新版本，以便充分利用Android新版本的特性。举例来说，设置这个值为11或更高，当你的应用运行在Android3.0或更高的系统上时，系统会为你的应用使用新的默认主题（Holo主题），并且当运行在大屏幕的设备上时会禁用屏幕兼容模式（screen compatibility mode），因为支持了 API level 11就暗示了支持大屏幕。

## 附录
[http://www.jianshu.com/p/02cb9a0eb2a0](Android Studio中Gradle使用详解)