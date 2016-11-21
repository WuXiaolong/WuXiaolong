---
title: Android App Shortcuts
date: 2016-10-31 21:49:53
tags: AppShortcuts
category: SupportLibrary
---
# 简介
Android 7.1允许您定义应用程序中特定操作的快捷方式。这些快捷键可以显示桌面，例如Nexus和Pixel设备。快捷键可让您的用户在应用程序中快速启动常见或推荐的任务。
每个快捷键引用一个或多个意图，每个意图在用户选择快捷方式时在应用程序中启动特定操作。可以表达为快捷方式的操作示例包括：
<!--more-->
在跳转页面时将用户导航到特定位置。
在通讯应用程式中传送讯息给朋友。
在媒体应用中播放电视节目的下一集。
在游戏应用程序中加载最后一个保存点。

App Shortcuts，一次最多可为您的应用发布4个快捷方式，当超过4个时，只显示最新四个，动态添加会抛**Max number of dynamic shortcuts exceeded**。但是，用户可以将应用的快捷方式复制到启动器上，从而创建固定的快捷方式。用户可以创建和访问无限数量的固定快捷方式，以触发应用中的操作。

更多介绍：https://developer.android.com/preview/shortcuts.html

# 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/AppShortcuts1.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
说明：需要长按桌面图标，然后就可以定义进入自己想要的页面了

![](http://7q5c2h.com1.z0.glb.clouddn.com/AppShortcuts2.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
说明：可以长按拖出创建一个固定的快捷方式

# 使用方法
## xml实现
1、AndroidManifest.xml
启动页，添加**meta-data**标签
```xml
<activity
    android:name=".MainActivity"
    android:label="@string/app_name"
    android:screenOrientation="portrait">
    <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>
    <meta-data
        android:name="android.app.shortcuts"
        android:resource="@xml/shortcuts"/>
</activity>
```
2、res/xml/shortcuts.xml
```xml
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
    <shortcut
        android:enabled="true"
        android:icon="@drawable/ic_content_copy_24dp"
        android:shortcutDisabledMessage="@string/shortcut_disabled_message1"
        android:shortcutId="shortcutId1"
        android:shortcutLongLabel="@string/shortcut_long_label1"
        android:shortcutShortLabel="@string/shortcut_short_label1">
        <intent
            android:action="action1"
            android:targetClass="com.wuxiaolong.designsupportlibrarysample.AppShortcutsActivity"
            android:targetPackage="com.wuxiaolong.designsupportlibrarysample"/>
    </shortcut>
    <shortcut
        android:enabled="true"
        android:icon="@drawable/ic_share_24dp"
        android:shortcutDisabledMessage="@string/shortcut_disabled_message1"
        android:shortcutId="shortcutId2"
        android:shortcutLongLabel="@string/shortcut_long_label2"
        android:shortcutShortLabel="@string/shortcut_short_label2">
        <intent
            android:action="action2"
            android:targetClass="com.wuxiaolong.designsupportlibrarysample.BottomNavigationActivity"
            android:targetPackage="com.wuxiaolong.designsupportlibrarysample"/>
    </shortcut>

</shortcuts>
```
说明：android:shortcutLongLabel和android:shortcutShortLabel，显示文本，默认显示long，当long很长，就显示short；android:targetClass跳转的页面；android:targetPackage包名

## 代码实现
添加App Shortcuts
```java
ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);
ShortcutInfo shortcut = new ShortcutInfo.Builder(mActivity, "shortcutId3")
        .setShortLabel("Web site")
        .setLongLabel("Open the web site")
        .setIcon(Icon.createWithResource(mActivity, R.drawable.ic_link_24dp))
        .setIntent(new Intent(Intent.ACTION_VIEW, Uri.parse("http://wuxiaolong.me/")))
        .build();
try {
    shortcutManager.setDynamicShortcuts(Arrays.asList(shortcut));
} catch (Exception e) {
    e.printStackTrace();
}
```

删除App Shortcuts
```java
ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);
List<ShortcutInfo> shortcutInfoList = shortcutManager.getDynamicShortcuts();//可以做个list管理App Shortcuts，这里略
List<String> list = new ArrayList<>();
list.add("shortcutId3");
try {
    shortcutManager.removeDynamicShortcuts(list);
} catch (Exception e) {
    e.printStackTrace();
}
```
隐藏App Shortcuts
```java
 ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);
 List<String> list = new ArrayList<>();
 list.add("shortcutId3");
 try {
     shortcutManager.disableShortcuts(list);
 } catch (Exception e) {
     e.printStackTrace();
 }
```
**这样就实现了App Shortcuts效果了**。

# 源码
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)

# 感谢
[官方文档](https://developer.android.com/preview/shortcuts.html)
[pcevikogullari/AndroidShortcuts](https://github.com/pcevikogullari/AndroidShortcuts)

# 最后
App Shortcuts只能在Android 7.1手机才有的效果，很炫很便捷，也不知道国内手机什么时候能看到App Shortcuts真容。