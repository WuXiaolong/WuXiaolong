title: Android打包错误Export aborted because fatal lint errors were found
date: 2013-11-22 
categories: [Android]
tags: [Android]
---
打包时报如下错误：
![](http://wuxiaolong.qiniudn.com/2013-11-22-Export-aborted-because-fatal-lint-errors-were-found-1.jpg)

<!-- more -->

Export aborted because fatal lint errors were found. These are listed in the Lint View. Either fix these before running Export again,or turn off"Run full error check when exporting app" in the Android > Lint Error Checking preference page.

解决方法如下： 

1、选择Project->Properties，弹出“Properties for 你的项目名称” 
![](http://wuxiaolong.qiniudn.com/2013-11-22-Export-aborted-because-fatal-lint-errors-were-found-3.jpg)
2、选择Adnroid Lint Preferences->Configure Workspace Settings...,弹出Preferences(Filtered)对话框，将"Run full error check when exporting app and abort if fatal errors are found "前的勾去掉，点击Apply再点击OK. 
![](http://wuxiaolong.qiniudn.com/2013-11-22-Export-aborted-because-fatal-lint-errors-were-found-2.jpg)
3、回到项目右击选择Android Tools->Clean Lint Markers,待项目自己更新完成即可。 
这时就可以打包了。
