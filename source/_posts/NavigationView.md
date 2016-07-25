title: Android Design Support Library之NavigationView
date: 2015-11-17 13:31:07
tags: NavigationView
category: SupportLibrary
---
# 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/designsupportlibrarysample.gif)

<!--more-->

# 使用方法
Gradle 中添加下面依赖
```xml
compile 'com.android.support:design:23.1.0'
```

# NavigationView
之前Google也提出了使用DrawerLayout来实现导航抽屉。这次，在support library中，Google提供了NavigationView来实现导航菜单界面。
```xml
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
     xmlns:app="http://schemas.android.com/apk/res-auto"
     android:id="@+id/drawer_layout"
     android:layout_width="match_parent"
     android:layout_height="match_parent"
     android:fitsSystemWindows="true">

     <!-- Your contents -->

     <android.support.design.widget.NavigationView
         android:id="@+id/navigation"
         android:layout_width="wrap_content"
         android:layout_height="match_parent"
         android:layout_gravity="start"
         app:headerLayout="@layout/drawer_header"
         app:itemTextColor="@color/item_text_color"
         app:menu="@menu/drawer" />
 </android.support.v4.widget.DrawerLayout>
```
 app:itemTextColor、 app:itemBackground分别设置item字体和背景颜色
 
menu/drawer
```xml
<?xml version="1.0" encoding="utf-8"?>

<menu xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
	<!--group 可设置单选效果-->
    <group android:checkableBehavior="single">

        <item
            android:id="@+id/navigation_item_1"
            android:checked="true"
            android:icon="@drawable/abc_btn_radio_material"
            android:title="微言-晨读励志" />

        <item
            android:id="@+id/navigation_item_2"
            android:icon="@drawable/abc_btn_radio_material"
            android:title="AndroidProgrammer" />

    </group>

    <item android:title="其他">

        <menu>

            <item
                android:icon="@drawable/abc_btn_radio_material"
                android:title="Android" />

            <item
                android:icon="@drawable/abc_btn_radio_material"
                android:title="IOS" />
        </menu>
    </item>

</menu>
```
drawer_header.xml
```xml
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
        android:text="@string/author"
        android:textColor="@color/white"
        android:textSize="20sp" />

</LinearLayout>
```
color/ item_text_color
```xml
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
    //设置头像，布局app:headerLayout="@layout/drawer_header"所指定的头布局
    View view = navigationView.inflateHeaderView(R.layout.drawer_header);
    TextView userName = (TextView) view.findViewById(R.id.userName);
    userName.setText(R.string.author);
    //View mNavigationViewHeader = View.inflate(MainActivity.this, R.layout.drawer_header, null);
    //navigationView.addHeaderView(mNavigationViewHeader);//此方法在魅族note 1，头像显示不全
    //菜单点击事件
    navigationView.setNavigationItemSelectedListener(new NavigationItemSelected());
}
```
NavigationItemSelected
```java
class NavigationItemSelected implements NavigationView.OnNavigationItemSelectedListener {
    @Override
    public boolean onNavigationItemSelected(MenuItem menuItem) {
        //mToolbar.setTitle(menuItem.getTitle());
        mDrawerLayout.closeDrawers();
        switch (menuItem.getItemId()) {
            case R.id.navigation_item_1:
                menuItem.setChecked(true);
                return true;
            case R.id.navigation_item_2:
                menuItem.setChecked(true);
                return true;
            default:
                return true;
        }
    }
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
    mDrawerLayout.addDrawerListener(mDrawerToggle);
}
```



# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidProgrammerLogo.jpg)

# 源码地址
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)

# 官网API
[https://developer.android.com/reference/android/support/design/widget/NavigationView.html](https://developer.android.com/reference/android/support/design/widget/NavigationView.html)