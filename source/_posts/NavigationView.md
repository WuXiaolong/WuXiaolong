title: Android Design Support Library之NavigationView
date: 2015-11-17 13:31:07
tags: [DesignSupportLibrary,NavigationView]
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
        app:headerLayout="@layout/drawer_header"
        app:itemTextColor="@color/item_text_color"
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
drawer_header.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:background="?attr/colorPrimaryDark"
    android:gravity="center"
    android:orientation="vertical">

    <ImageView
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_marginTop="10dp"
        android:background="@mipmap/ic_launcher" />

    <TextView
        android:id="@+id/userName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:gravity="center"
        android:text="小尛龙"
        android:textColor="@color/white"
        android:textSize="20sp" />

</LinearLayout>
```
@color/ item_text_color
```js
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="@color/primary" android:state_checked="true" />
    <item android:color="@color/black" />
</selector>
```
其中最重要的就是这两个属性：

app:headerLayout 
app:menu

通过这两个属性，我们可以非常方便的指定导航界面的头布局和菜单布局。
```java
private NavigationView navigationView;
private void initNavigationViewHeader() {
        navigationView = (NavigationView) findViewById(R.id.navigation);
       //设置头像，布局app:headerLayout所指定的头布局
       View view = navigationView.inflateHeaderView(R.layout.drawer_header);
        TextView userName = (TextView) view.findViewById(R.id.userName);
        userName.setText("小尛龙");
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
设置DrawerLayout
```java
private DrawerLayout mDrawerLayout;
private void initDrawer() {
        mDrawerLayout = (DrawerLayout) findViewById(R.id.drawer_layout);
        mDrawerToggle = new ActionBarDrawerToggle(this, mDrawerLayout, mToolbar, R.string.open, R.string.close) {
            @Override
            public void onDrawerOpened(View drawerView) {
                super.onDrawerOpened(drawerView);
            }

            @Override
            public void onDrawerClosed(View drawerView) {
                super.onDrawerClosed(drawerView);
            }
        };
        mDrawerToggle.syncState();
        mDrawerLayout.setDrawerListener(mDrawerToggle);
    }
```
## 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>

## 源码地址
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)
