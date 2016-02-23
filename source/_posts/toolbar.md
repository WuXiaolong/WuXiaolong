title: Android Toolbar
date: 2015-11-10 10:18:18
tags: Toolbar
category: Android
---
在Material Design之中有一个名称为Toolbar，用来取代过去的ActionBar控件。
# 基本用法
可分为三步：
## 新增ToolBar样式
value/styles.xml：
```js
 <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
  <item name="colorPrimary">@color/colorPrimary</item>
  <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
  <item name="colorAccent">@color/colorAccent</item>
  <item name="android:textColorPrimary">@android:color/white</item>
  <!--返回或更多(app:showAsAction="never")的按钮颜色-->
  <item name="colorControlNormal">@android:color/white</item>
  <!--菜单字体颜色-->
  <item name="actionMenuTextColor">@android:color/white</item>
 </style>
```
看看 parent="Theme.AppCompat.Light.NoActionBar"
```js
 <style name="Theme.AppCompat.Light.NoActionBar">
        <item name="windowActionBar">false</item>
        <item name="windowNoTitle">true</item>
 </style>
```
<!--more-->
## XML布局中新增ToolBar
```js
<android.support.v7.widget.Toolbar 
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?actionBarSize"
    android:background="?attr/colorPrimary"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light">
 </android.support.v7.widget.Toolbar>
```
## 在程序中替代ActionBar
```java
 Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        android.support.v7.app.ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(true);
             
        }
```
res/menu/activity_main.xml
```js
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      xmlns:tools="http://schemas.android.com/tools"
      tools:context=".MainActivity">
 
  <item android:id="@+id/action_edit"
        android:title="@string/action_edit"
        android:orderInCategory="80"
        android:icon="@drawable/ab_edit"
        app:showAsAction="ifRoom" />
</menu>
```

```java
 @Override
    public boolean onCreateOptionsMenu(Menu menu) {

        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.activity_main, menu);
        return true;
    }
 @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {

            case R.id.action_edit:
              return true;
            case android.R.id.home:
              super.onBackPressed();//返回
              return true;
            default:
                return super.onOptionsItemSelected(item);
        }

    }
```
## 单独使用而不与ActionBar进行关联
在前一节中设置与ActionBar进行关联，如果不进行关联也是可以使用。即执行方法：setSupportActionBar，那么Menu的操作也不用在onCreateOptionsMenu方法，直接使用ToolBar的inflateMenu方法，Menu的事件也是独立的，需要通过设置ToolBar的setOnMenuItemClickListener来实现。
```java
Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
 
// App Logo
toolbar.setLogo(R.drawable.ic_launcher);
// Title
toolbar.setTitle("My Title");
// Sub Title
toolbar.setSubtitle("Sub title");
 
//setSupportActionBar(toolbar);
 toolbar.inflateMenu(R.menu.activity_main);
// Navigation Icon 要設定在 setSupoortActionBar后 才有作用
// 否則會出現 back button 
toolbar.setNavigationIcon(R.drawable.ab_android);
toolbar.setNavigationOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                
            }
        });
// Menu item click 设定在 setSupportActionBar 之后才有作用
toolbar.setOnMenuItemClickListener(onMenuItemClick);
```
onMenuItemClick
```java
private Toolbar.OnMenuItemClickListener onMenuItemClick = new Toolbar.OnMenuItemClickListener() {
  @Override
  public boolean onMenuItemClick(MenuItem menuItem) {
    String msg = "";
    switch (menuItem.getItemId()) {
      case R.id.action_edit:  
        break;
    }
    return true;
  }
};
```
# 自定义布局
 title修改为居中
```js
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:background="@mipmap/bg_title"
    android:minHeight="?actionBarSize">

    <TextView
        android:id="@+id/toolbar_title"
        style="@style/TextAppearance.AppCompat.Widget.ActionBar.Title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center" />
</android.support.v7.widget.Toolbar>
```

禁用系统的title显示，即setDisplayShowTitleEnabled方法
```java
 Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
  TextView  mTitleView = (TextView) toolbar.findViewById(R.id.toolbar_title);
        android.support.v7.app.ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(true);
             actionBar.setDisplayShowTitleEnabled(false);
        }
  mTitleView.setText("");
```
# 实现将布局的内容延伸到状态栏
style
```js 
 <item name="android:windowTranslucentStatus" tools:targetApi="19">true</item>
```
Toolbar增加一个paddingTop:
```js
<android.support.v7.widget.Toolbar 
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@mipmap/bg_title"
    android:minHeight="?actionBarSize"
    android:paddingTop="@dimen/status_bar_height"/>
```
values/dimens.xml
```js
<dimen name="status_bar_height">0dp</dimen>
```
values-v19/dimens.xml
```js
<dimen name="status_bar_height">25dp</dimen>
```
values-v23/dimens.xml
```js
<dimen name="status_bar_height">24dp</dimen>
```
可以通过以下方法取得StatusBarHeight：
```java
public int getStatusBarHeight() {
        int result = 0;
        int resourceId = getResources().getIdentifier("status_bar_height", "dimen", "android");
        if (resourceId > 0) {
            result = getResources().getDimensionPixelSize(resourceId);
        }
        return result;
    }
```
`另外注意：`toolbar所在的父布局不能同时设置fitsSystemWindow="true"，它会使得屏幕上的可布局空间位于状态栏下方与导航栏上方。

因为这个带来了键盘挡住EditText的问题，解决方案：
在EditText所在的父布局设置fitsSystemWindow="true"即可。

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 附录
[Android ToolBar Widget Usage](http://blog.hwangjr.com/2015/07/10/Android-ToolBar-Widget-Usage/)
[ANDROID – TOOLBAR STEP BY STEP](http://blog.mosil.biz/2014/10/android-toolbar/)

