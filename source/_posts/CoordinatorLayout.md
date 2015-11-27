title: Android Design Support Library之CoordinatorLayout
date: 2015-11-17 14:18:00
tags: [DesignSupportLibrary,CoordinatorLayout]
category: Android 
---
## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/designsupportlibrarysample.gif)

<!--more-->

## 实例App
![](http://7q5c2h.com1.z0.glb.clouddn.com/dashishuoDownload.png)
## 使用方法
Gradle 中添加下面依赖
```js
 compile 'com.android.support:design:23.1.0'
```
## CoordinatorLayout
```js
<android.support.design.widget.CoordinatorLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

     <! --Your Scrollable View 只支持RecyclerView和NestedScrollView-->
    <android.support.v7.widget.RecyclerView
            android:layout_width="match_parent"
            android:layout_height="match_parent"                   app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
            <android.support.v7.widget.Toolbar
                  android:id="@+id/toolbar"
                  android:layout_width="match_parent"
                  android:layout_height="?attr/actionBarSize"
                  android:background="@color/primary"
                  app:layout_scrollFlags="scroll|enterAlways">

            <android.support.design.widget.TabLayout
                  android:layout_width="match_parent"
                  android:layout_height="wrap_content"
                  app:layout_scrollFlags="scroll|enterAlways">
     </android.support.design.widget.AppBarLayout>
</android.support.design.widget.CoordinatorLayout>
```
* 给这个可滚动组件设置了layout_behavior 
* 给另一个控件设置了layout_scrollFlags 
当设置了layout_behavior的控件滑动时，就会触发设置了layout_scrollFlags的控件发生状态的改变。
设置的layout_scrollFlags有如下几种选项：

* scroll: 所有想滚动出屏幕的view都需要设置这个flag- 没有设置这个flag的view将被固定在屏幕顶部。
* enterAlways: 这个flag让任意向下的滚动都会导致该view变为可见，启用快速“返回模式”。
* enterAlwaysCollapsed: 当你的视图已经设置minHeight属性又使用此标志时，你的视图只能已最小高度进入，只有当滚动视图到达顶部时才扩大到完整高度。
* exitUntilCollapsed: this flag causes the view to scroll off until it is ‘collapsed’ (its minHeight) before exiting。

## 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>

## 源码地址
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)
