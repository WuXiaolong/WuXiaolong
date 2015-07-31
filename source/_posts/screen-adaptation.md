title: Android屏幕适配不同的资源图片、布局
date: 2013-07-23 
categories: [Android]
tags: [Android]
---
在Android 2.1版本之前，存放资源图片的文件夹名称为drawable，应用程序用到的所有的资源图片都放在其中，所以很难解决不同屏幕大小的图片适配问题。

1、资源图片

自android 2.1版本开始，存放资源图片的文件夹由原来的一个drawable文件夹变成了drawable-hdpi、drawable-mdpi和drawable-ldpi，这是为了支持不同终端设备的不同分辨率。

drawable-hdpi、drawable-mdpi、drawable-ldpi的区别：

（1）drawable-hdpi里面存放高分辨率的图片，如WVGA(480x800)，FWVGA(480x854)

（2）drawable-mdpi里面存放中等分辨率的图片，如(320x480)

（3）drawable-ldpi里面存放低分辨率的图片，如QVGA(240x320)

<!-- more -->

系统会根据机器的分辨率来分别到这几个文件夹里面去找对应的图片，同一资源的不同分辨率的图片应该命以相同的名称存放在不同的资源图片文件夹中。

2、布局的适配：

在第1点中我们已经解决了资源图片的适配问题，但是图片的大小不同势必会造成布局的差异，如果使用一个统一的布局文件，
可能会造成资源图片无法完整显示在屏幕内，所以对于这种问题，解决方法是建立不同的layout文件夹，对于不同屏幕的分辨率，书写其专用的.xml文件。

多个layout文件夹在命名上有相应规则，以分辨率480x854为例，需要建立480x854像素的layout文件夹，命名：layout-854x480，

有两点需要`注意`：①大数（854）必须在前，否则会报错；②两个数字之间的符号是小写英文字母“x”，不是乘号。系统会根据机器的分辨率来分别到这几个文件夹里面去找对应的布局文件。如下：
![布局文件](http://wuxiaolong.qiniudn.com/2013-07-23-screen-adaptation-1.jpg)

如果你建立的layout文件夹的分辨率是你的eclipse加载的android工具所支持的分辨率，那么在可视化的布局界面中会自动给出屏幕分辨率、屏幕尺寸（红色箭头处）等参数，值得注意的是，如果红色箭头处是“Custom…”，那么你就要人为对其进行修改，选择合适的屏幕尺寸。
![选择合适的屏幕尺寸](http://wuxiaolong.qiniudn.com/2013-07-23-screen-adaptation-2.jpg)

3、设置屏幕的横竖屏显示：
如果不做特殊说明，开发的应用是横竖屏重力敏感的，如果禁止横竖屏切换，需要在AndroidManifest.xml文件中做相应的修改：

```java
	<activity android:name=".MenuView"
	android:label="@string/app_name"
	android:screenOrientation="portrait">
```
在activity的属性中添“android:screenOrientation="portrait"”，portrait表示竖屏，横屏则用landscape表示。

4、dip和px的问题：

在网上找了好多关于“dip和px的区别”的资料，但是解释的都不是很清楚。
我很肤浅的理解：dip是相对的，px是绝对的，所以dip能够有效解决不同型号手机不同分辨率的布局适配问题。

但是由于在第2点中已经用不同layout文件夹解决了布局适配的问题，所以在其中使用dip就是没有意义的，甚至更加麻烦。
既然已经分了不同的布局文件，所以在各个布局文件中直接使用px单位就可以了。
