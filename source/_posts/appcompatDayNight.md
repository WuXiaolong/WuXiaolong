title: Android Support Library 之 夜间模式
date: 2016-07-12 23:12:23
tags: NightTheme
category: SupportLibrary
---
# 前言
夜间模式实现方式
1、通过切换theme来实现夜间模式。
优点：可以匹配多套主题，并不局限于黑白模式
缺点：需要大量定义主题
详见博客：http://wuxiaolong.me/2015/08/19/ChangeTheme/

2、通过修改uiMode来切换夜间模式。
修改uimode是修改Configuration，这种主题切换只限于黑白模式，没有其他模式，不需要大量定义主题，即本文介绍的内容。

# 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/nightTheme.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
<!--more-->
# 如何使用
## 第一步 
（1）app/build.gradle
```java
compile 'com.android.support:appcompat-v7:24.0.0'
```
（2）Activity须继承AppCompatActivity

（3）Theme.AppCompat.DayNight
```java
 <style name="AppTheme" parent="Theme.AppCompat.DayNight">
     <!-- Customize your theme here. -->
     <item name="colorPrimary">@color/colorPrimary</item>
     <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
     <item name="colorAccent">@color/colorAccent</item>
 </style>
 <style name="AppTheme.NoActionBar">
     <item name="windowActionBar">false</item>
     <item name="windowNoTitle">true</item>
 </style>
```
## 第二步
应用全局主题推荐在 Application 的onCreate()中进行设置
AppCompatDelegate.setDefaultNightMode(int mode);

它有四个可选值，分别是：
MODE_NIGHT_NO： 使用亮色(light)主题，不使用夜间模式
MODE_NIGHT_YES：使用暗色(dark)主题，使用夜间模式
MODE_NIGHT_AUTO：根据当前时间自动切换 亮色(light)/暗色(dark)主题
MODE_NIGHT_FOLLOW_SYSTEM(默认选项)：设置为跟随系统，通常为 MODE_NIGHT_NO

## 第三步
动态的设定主题，需要切换主题调用：
```java
AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_YES);
//调用recreate()使设置生效
recreate();
```

# 微信公众号
我的微信公众号：吴小龙同学。不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 注意事项
## setDefaultNightMode()与setLocalNightMode()区别
AppCompatDelegate.setDefaultNightMode()是对整个App中theme为DayNight主题生效
getDelegate().setLocalNightMode()只对特定的组件生效

## 夜间资源
把夜晚主题的color等资源放在values-night中，程序在运行时就会自动调用

## 获取应用当前的主题
```java
int currentNightMode = getResources().getConfiguration().uiMode
        & Configuration.UI_MODE_NIGHT_MASK;
switch (currentNightMode) {
    case Configuration.UI_MODE_NIGHT_NO:
        // Night mode is not active, we're in day time
    case Configuration.UI_MODE_NIGHT_YES:
        // Night mode is active, we're at night!
    case Configuration.UI_MODE_NIGHT_UNDEFINED:
        // We don't know what mode we're in, assume notnight
}
```
如果切换了主题，本想通过这个方法，下次启动程序的时候，来设置。发现并没有记住这个值，还是只能SharedPreference读取，然后根据用户设置，调用 setDefaultNightMode() 方法。

## 适配文字图片
尽可能的使用主题属性（theme attributes）

**文字颜色**
```
android:textColor="?android:attr/textColorPrimary" 
android:textColor="?android:textColorPrimary"
```
系统默认的文字颜色。在亮色（light）主题下，颜色接近黑色，在暗色（dark）主题下，颜色接近白色。有两种写法，次标题和三标题同样。

次标题
```
android:textColor="?android:attr/textColorSecondary" 

```

三标题
```
android:textColor="?android:attr/textColorTertiary" 
```

**系统默认的背景颜色**
```
android:background="?android:attr/colorBackground"
android:background="?android:colorBackground"
```

**点击效果**
```
android:background="?android:attr/selectableItemBackground"
android:background="?android:selectableItemBackground"
android:background="?attr/selectableItemBackground"
```

**系统默认的图标颜色**
```
?attr/colorControlNormal
```

# 源码地址
https://github.com/WuXiaolong/DesignSupportLibrarySample
