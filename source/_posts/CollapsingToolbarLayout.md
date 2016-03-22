title: Android Design Support Library之CollapsingToolbarLayout
date: 2015-11-17 14:18:22
tags: [DesignSupportLibrary,CollapsingToolbarLayout]
category: DesignSupportLibrary
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
## CollapsingToolbarLayout
```js
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:context="com.wuxiaolong.designsupportlibrarysample.DetailActivity">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:layout_height="350dp"
        android:fitsSystemWindows="true"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:contentScrim="?attr/colorPrimary"
            app:expandedTitleMarginEnd="64dp"
            app:expandedTitleMarginStart="48dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">

            <ImageView
                android:id="@+id/backdrop"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:fitsSystemWindows="true"
                android:scaleType="centerCrop"
                android:src="@mipmap/show_img"
                app:layout_collapseMode="parallax" />

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin" />


        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>


    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingTop="24dp"
            android:text="@string/show_text" />
    </android.support.v4.widget.NestedScrollView>

    <android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="@dimen/fab_margin"
        android:src="@android:drawable/ic_dialog_dialer"
        app:layout_anchor="@id/appbar"
        app:layout_anchorGravity="bottom|right|end" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="@dimen/fab_margin"
        android:src="@android:drawable/ic_dialog_email" />

</android.support.design.widget.CoordinatorLayout>

```
* app:layout_scrollFlags="scroll|exitUntilCollapsed"
这是两个Flag控制滚动时候CollapsingToolbarLayout的表现。 Scroll，表示向下滚动列表时候，CollapsingToolbarLayout会滚出屏幕并且消失；exitUntilCollapsed，表示这个layout会一直滚动离开屏幕范围，直到它收折成它的最小高度

* app:contentScrim="?attr/colorPrimary"
设置折叠后Toolbar的颜色

*  app:layout_collapseMode="parallax"
设置视差模式。parallax，表示滚动过程中,会一直保持可见区域在正中间；pin，表示不会被滚出屏幕范围。也可以设置视差的系数app:layout_collapseParallaxMultiplier="0.7"，介于 0.0-1.0之间

* app:expandedTitleMargin、 app:expandedTitleMarginBottom、app:expandedTitleMarginEnd 、 app:expandedTitleMarginStart设置展开状态改变标题文字的位置

* app:collapsedTitleTextAppearance 和 app:expandedTitleTextAppearance 设置在折叠和展开状态时改变文本的显示

## 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>

## 源码地址
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)