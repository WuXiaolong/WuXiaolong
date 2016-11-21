---
title:  Android Design Support Library之BottomNavigation
date: 2016-10-21 20:34:57
tags: BottomNavigation
category: SupportLibrary
---
随着Android 7.1的的发布，相关的开发工具与套件也一起更新了，包括Android Studio 2.2.2与Support Library 25.0.0。这次Support Library实现了Material Design中的Bottom Navigation设计样式。
Bottom Navigation适合3-5个底部菜单，官方规范给的3个菜单设计图是这样的：
<!--more-->
![](http://7q5c2h.com1.z0.glb.clouddn.com/bottomNavigation1.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
4-5个菜单：
![](http://7q5c2h.com1.z0.glb.clouddn.com/bottomNavigation2.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

更多设计细节规范见：[https://material.google.com/components/bottom-navigation.html](https://material.google.com/components/bottom-navigation.html)

# Bottom Navigation使用
## 效果预览
3个菜单：
![](http://7q5c2h.com1.z0.glb.clouddn.com/bottomNavigation3.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
5个菜单：
![](http://7q5c2h.com1.z0.glb.clouddn.com/bottomNavigation4.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
## 使用方法
**xml**：
```
<android.support.design.widget.BottomNavigationView
    android:id="@+id/bottomNavigationView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:itemIconTint="@color/bottom_item_text_color"
    app:itemTextColor="@color/bottom_item_text_color"
    app:menu="@menu/menu_bottom_navigation_items"/>
```
app:itemIconTint：设置菜单图标颜色
app:itemTextColor：设置文本颜色

color/bottom_item_text_color.xml
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="?android:attr/textColorPrimary" android:state_checked="true"/>
    <item android:color="?android:attr/textColorPrimary" android:state_pressed="true"/>
    <item android:color="?android:attr/textColorSecondary"/>
</selector>
```
 app:menu：设置底部菜单 
 
menu/menu_bottom_navigation_items.xml
```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/item1"
        android:checked="true"
        android:icon="@drawable/ic_content_copy_24dp"
        android:title="Text1"/>

    <item
        android:id="@+id/item2"
        android:icon="@drawable/ic_add_24dp"
        android:title="Text2"/>

    <item
        android:id="@+id/item3"
        android:icon="@drawable/ic_link_24dp"
        android:title="Text3"/>
</menu>
```

**Java**
```
BottomNavigationView bottomNavigationView = (BottomNavigationView) findViewById(R.id.bottomNavigationView);
bottomNavigationView.setOnNavigationItemSelectedListener(new BottomNavigationView.OnNavigationItemSelectedListener() {
    @Override
    public boolean onNavigationItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()) {
            case R.id.item1:
                break;
            case R.id.item2:
                break;
            case R.id.item3:
                break;
        }
        return false;
    }
});
```
就是如此简单，可以愉快玩耍去了。

# Bottom Navigation源码
其实自己写个这样的底部菜单效果并不难，官方这个，我对那个点击有个动画效果比较感兴趣，应该是一点击就是对图标和文字进行缩放动画，Bottom Navigation源码涉及4个类BottomNavigationView、BottomNavigationPresenter、BottomNavigationMenuView、BottomNavigationItemView，缩放动画在BottomNavigationItemView里，核心代码：
```
@Override
public void setChecked(boolean checked) {
    mItemData.setChecked(checked);
    ViewCompat.setPivotX(mLargeLabel, mLargeLabel.getWidth() / 2);
    ViewCompat.setPivotY(mLargeLabel, mLargeLabel.getBaseline());
    ViewCompat.setPivotX(mSmallLabel, mSmallLabel.getWidth() / 2);
    ViewCompat.setPivotY(mSmallLabel, mSmallLabel.getBaseline());
    if (mShiftingMode) {
        if (checked) {
            LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams()
            iconParams.gravity = Gravity.CENTER_HORIZONTAL | Gravity.TOP;
            iconParams.topMargin = mDefaultMargin;
            mIcon.setLayoutParams(iconParams);
            mLargeLabel.setVisibility(VISIBLE);
            ViewCompat.setScaleX(mLargeLabel, 1f);
            ViewCompat.setScaleY(mLargeLabel, 1f);
        } else {
            LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams()
            iconParams.gravity = Gravity.CENTER;
            iconParams.topMargin = mDefaultMargin;
            mIcon.setLayoutParams(iconParams);
            mLargeLabel.setVisibility(INVISIBLE);
            ViewCompat.setScaleX(mLargeLabel, 0.5f);
            ViewCompat.setScaleY(mLargeLabel, 0.5f);
        }
        mSmallLabel.setVisibility(INVISIBLE);
    } else {
        if (checked) {
            LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams()
            iconParams.gravity = Gravity.CENTER_HORIZONTAL | Gravity.TOP;
            iconParams.topMargin = mDefaultMargin + mShiftAmount;
            mIcon.setLayoutParams(iconParams);
            mLargeLabel.setVisibility(VISIBLE);
            mSmallLabel.setVisibility(INVISIBLE);
            ViewCompat.setScaleX(mLargeLabel, 1f);
            ViewCompat.setScaleY(mLargeLabel, 1f);
            ViewCompat.setScaleX(mSmallLabel, mScaleUpFactor);
            ViewCompat.setScaleY(mSmallLabel, mScaleUpFactor);
        } else {
            LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams()
            iconParams.gravity = Gravity.CENTER_HORIZONTAL | Gravity.TOP;
            iconParams.topMargin = mDefaultMargin;
            mIcon.setLayoutParams(iconParams);
            mLargeLabel.setVisibility(INVISIBLE);
            mSmallLabel.setVisibility(VISIBLE);
            ViewCompat.setScaleX(mLargeLabel, mScaleDownFactor);
            ViewCompat.setScaleY(mLargeLabel, mScaleDownFactor);
            ViewCompat.setScaleX(mSmallLabel, 1f);
            ViewCompat.setScaleY(mSmallLabel, 1f);
        }
    }
    refreshDrawableState();
}
```
这里设计一个小的文本mSmallLabel和大的文本mLargeLabel，默认大的是隐藏的，xml如下：
```
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="24dp"
        android:layout_height="24dp"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="@dimen/design_bottom_navigation_margin"
        android:layout_marginBottom="@dimen/design_bottom_navigation_margin"
        android:duplicateParentState="true" />
    <android.support.design.internal.BaselineLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="@dimen/design_bottom_navigation_margin"
        android:layout_gravity="bottom|center_horizontal"
        android:duplicateParentState="true">
        <TextView
            android:id="@+id/smallLabel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="@dimen/design_bottom_navigation_text_size"
            android:duplicateParentState="true" />
        <TextView
            android:id="@+id/largeLabel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:visibility="invisible"
            android:textSize="@dimen/design_bottom_navigation_active_text_size"
            android:duplicateParentState="true" />
    </android.support.design.internal.BaselineLayout>
</merge>
```
mShiftingMode是否是3个菜单标识。
看代码才知道，图标并没有做缩放动画，当4/5个菜单时，文本和图标有6dp距离，即mShiftAmount值，这点设计图有标哦。
没有checked时，图标margin是8dp，当文本mLargeLabel显示，图标和文本margin是6dp，造成图标也缩放的错觉。
其他代码都很简单，不注释了。
好了，如果对Bottom Navigation源码感兴趣，可自行去查看。
# demo地址
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)，下载运行，Bottom Navigation入口：左侧菜单-其他组件-Bottom navigation，over。

# 推荐阅读
[http://wuxiaolong.me/categories/SupportLibrary/](http://wuxiaolong.me/categories/SupportLibrary/)