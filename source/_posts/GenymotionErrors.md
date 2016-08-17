title: Android Studio和Genymotion安装常见错误
date: 2016-03-17 19:47:04
tags: [Android, Android Studio,Genymotion]
category: Android 
---
> 整装出发，新的环境，新的开始，加油，骚年。新公司，首先需要安装Android的开发环境，由于被墙，Android Studio首次启动就被卡，Genymotion也遇到了些问题。

# Android Studio初次启动卡在download Components
![](http://7q5c2h.com1.z0.glb.clouddn.com/GenymotionErrors3.jpg)
<!--more-->
解决方案:

详见：[http://blog.csdn.net/u010302764/article/details/42889731](http://blog.csdn.net/u010302764/article/details/42889731)

# Genymotion下载虚拟镜像Connection timeout
Add new device出现的问题：
```
Failed to deploy virtual device.
Unable to create virtual device.Connection timeout occurred.
```

解决方案：

1. 当选择Google Nexus 5 - 6.0.0 - API 23 - 1080x1920下载device失败后，到C:\Users\用户主目录\AppData\Local\Genymobile\Genymotion\ova下看到genymotion_vbox86p_6.0_160114_090449.ova，大小却是0KB，在C:\Users\用户主目录\AppData\Local\Genymobile\genymotion.log，打开该文件，找到类似“http://files2.genymotion.com/dists/6.0.0/ova/genymotion_vbox86p_6.0_160114_090449.ova”路径，即您想要下载的镜像文件URL；

2. 用迅雷去下载，下载完成后放到C:\Users\用户主目录\AppData\Local\Genymobile\Genymotion\ova；

3. 重新点击Google Nexus 5 - 6.0.0 - API 23 - 1080x1920去下载，验证安装后即会显示在设备列表中。

# virtualbox cannot start the virtual device
![](http://7q5c2h.com1.z0.glb.clouddn.com/GenymotionErrors1.jpg)

然后去vbox运行虚拟机，报如下错误：
![](http://7q5c2h.com1.z0.glb.clouddn.com/GenymotionErrors2.jpg)
点击复制看具体错误信息：
```
不能为虚拟电脑 Google Nexus 5 - 6.0.0 - API 23 - 1080x1920 打开一个新任务.

Unable to load R3 module D:\Program Files\Oracle\VirtualBox/VBoxDD.DLL (VBoxDD): GetLastError=1790 (VERR_UNRESOLVED_ERROR).

返回 代码:E_FAIL (0x80004005)
组件:ConsoleWrap
界面:IConsole {872da645-4a9b-1727-bee2-5585105b9eed}
```
原因:
宿主机win7用的ghost系统，会破解uxtheme.dll文件，导致virtualbox启动失败

解决方案:
去网上下载Windows7 64bit uxtheme.dll，使用原版的uxtheme.dll替换c:\windows\system32\uxtheme.dll即可。

# x86 emulation currently requires hardware acceleration
emulator: ERROR: x86 emulation currently requires hardware acceleration!
Please ensure Intel HAXM is properly installed and usable.
CPU acceleration status: HAX kernel module is not installed!
解决方案
详见我的博客：[Android之emulator ERROR x86 emulation currently requires hardware acceleration](http://wuxiaolong.me/2015/12/03/emulatorError/)

# 其他常用的软件
安装自己平时常用的软件，将公司电脑环境变成自己的风格，详见我的博客：[我的编码嗜好](http://wuxiaolong.me/2016/03/13/MyCodeHobby/)

# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)
	

