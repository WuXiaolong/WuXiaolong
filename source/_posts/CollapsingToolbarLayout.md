title: Android Design Support Library之CollapsingToolbarLayout
date: 2015-11-17 14:18:22
tags: CollapsingToolbarLayout
category: SupportLibrary
---
## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/designsupportlibrarysample.gif)

<!--more-->

## 实例App
![](http://7q5c2h.com1.z0.glb.clouddn.com/dashishuoDownload.png)
## 使用方法
Gradle 中添加下面依赖
```java
compile 'com.android.support:design:23.1.0'
```
## CollapsingToolbarLayout
```java
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
说明
app:layout_scrollFlags="scroll|exitUntilCollapsed"
这是两个Flag控制滚动时候CollapsingToolbarLayout的表现。 
Scroll，表示向下滚动列表时候
CollapsingToolbarLayout会滚出屏幕并且消失
exitUntilCollapsed，表示这个layout会一直滚动离开屏幕范围，直到它收折成它的最小高度

设置折叠后Toolbar的颜色
app:contentScrim="?attr/colorPrimary"


app:layout_collapseMode="parallax"
设置视差模式。parallax，表示滚动过程中,会一直保持可见区域在正中间；pin，表示不会被滚出屏幕范围。
也可以设置视差的系数app:layout_collapseParallaxMultiplier="0.7"，介于 0.0-1.0之间

设置展开状态改变标题文字的位置
app:expandedTitleMargin、 app:expandedTitleMarginBottom、app:expandedTitleMarginEnd 、 app:expandedTitleMarginStart

设置在折叠和展开状态时改变文本的显示
app:collapsedTitleTextAppearance 和 app:expandedTitleTextAppearance 


FloatingActionButton

设置锚点属性
app:layout_anchor="@id/appbar"
意思是FAB浮动按钮显示在哪个布局区域。 

设置当前锚点的位置
app:layout_anchorGravity=”bottom|end|right”
意思FAB浮动按钮在这个布局区域的具体位置。 
两个属性共同作用才是的FAB 浮动按钮也能折叠消失，出现。

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 源码地址
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)

# 官网API
[https://developer.android.com/reference/android/support/design/widget/CollapsingToolbarLayout.html](https://developer.android.com/reference/android/support/design/widget/CollapsingToolbarLayout.html)