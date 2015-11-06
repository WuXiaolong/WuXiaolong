title: Android Design Support Library使用
date: 2015-11-06 13:08:01
tags: DesignSupportLibrary
category: Android
---
![](http://7q5c2h.com1.z0.glb.clouddn.com/dashishuoScreenshots.gif)

<!--more-->

## 实例App
![](http://7q5c2h.com1.z0.glb.clouddn.com/dashishuoDownload.png)

## NavigationView
之前Google也提出了使用DrawerLayout来实现导航抽屉。这次，在support library中，Google提供了NavigationView来实现导航菜单界面。
```js
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <!-- 你的内容布局-->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:layout_scrollFlags="scroll|enterAlways" />

        <FrameLayout
            android:id="@+id/content"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </LinearLayout>

    <android.support.design.widget.NavigationView
        android:id="@+id/navigation"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:itemTextColor="?attr/colorPrimary"
        app:menu="@menu/drawer">

    </android.support.design.widget.NavigationView>

</android.support.v4.widget.DrawerLayout>
```
@menu/drawer
```java
<?xml version="1.0" encoding="utf-8"?>

<menu xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <group android:checkableBehavior="single">

        <item
            android:id="@+id/navigation_item_1"
            android:icon="@drawable/ic_fa_home"
            android:title="今日情感" />
    </group>

    <item android:title="互动">

        <menu>
            <item
                android:id="@+id/ic_action_email"
                android:icon="@drawable/ic_action_email"
                android:title="意见反馈" />
            <item
                android:id="@+id/ic_action_share"
                android:icon="@drawable/ic_action_share"
                android:title="推荐好友" />
            <item
                android:id="@+id/ic_action_good"
                android:icon="@drawable/ic_action_good"
                android:title="好评鼓励" />

        </menu>
    </item>
    <item android:title="主题">

        <menu>
            <item
                android:id="@+id/ic_action_theme"
                android:icon="@drawable/ic_fa_eye"
                android:title="切换主题" />

        </menu>
    </item>

</menu>
```
其中最重要的就是这两个属性：

app:headerLayout 
app:menu

通过这两个属性，我们可以非常方便的指定导航界面的头布局和菜单布局。
```java
private void initNavigationViewHeader() {
        mNavigationViewHeader = LayoutInflater.from(this).inflate(R.layout.drawer_header, null, false);
       //设置头像，布局app:headerLayout所指定的头布局
        navigationView.addHeaderView(mNavigationViewHeader);
	  //菜单点击事件
        navigationView.setNavigationItemSelectedListener(new NavigationItemSelected());
    }
```
```java
class NavigationItemSelected implements NavigationView.OnNavigationItemSelectedListener {
        @Override
        public boolean onNavigationItemSelected(MenuItem menuItem) {
            mToolbar.setTitle(menuItem.getTitle());
            switch (menuItem.getItemId()) {
                case R.id.navigation_item_1:
                    selectItem(0);
                    menuItem.setChecked(true);
                    return true;               
                default:
                    return true;
            }
        }
    }
```
设置Fragment
```java
  private void selectItem(int position) {
        mDrawerLayout.closeDrawers();
        Fragment fragment = null;
        switch (position) {

            case 0:
                fragment = new HomeFragment().newInstance();
                break;
        }

        FragmentManager fragmentManager = getSupportFragmentManager();
        FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
        if (fragment != null && fragment.isAdded()) {
            fragmentTransaction.hide(mCurrentFragment).show(fragment);
        } else {
            fragmentTransaction.add(R.id.content, fragment);
        }
        mCurrentFragment = fragment;
        fragmentTransaction.commit();
    }
```

## CoordinatorLayout
```js
<android.support.design.widget.CoordinatorLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

     <! -- Your Scrollable View 只支持RecyclerView和 -->
    <android.support.v7.widget.RecyclerView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
            <android.support.v7.widget.Toolbar
                  ...
                  app:layout_scrollFlags="scroll|enterAlways">

            <android.support.design.widget.TabLayout
                  ...
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

## CollapsingToolbarLayout
