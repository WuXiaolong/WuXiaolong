title: Android Fragment使用Toolbar
date: 2015-12-21 10:34:49
tags: [Fragment,Toolbar]
category: Android 
---
# Activity使用Toolbar
一般在Activity里使用Toolbar如下：
toolbar.xml
```js 
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:minHeight="?actionBarSize">
</android.support.v7.widget.Toolbar>
```
xml调用：
```js
<include
     layout="@layout/toolbar" />
```
<!--more-->
Activity一般写到基类：
```java
 public Toolbar initToolbar(int title) {
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(true);
          }
        return toolbar;
    }

    public Toolbar initToolbar(CharSequence title) {
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(true);
        }
        return toolbar;
    }
```
Activity调用：
```java
  initToolbar("标题");
```
# Fragment使用Toolbar
现在项目需求，每个title不一样，就不能像以上一样写一个toolbar，放在最外层。问题来了，Fragment如何使用Toolbar？像上面一样写吗？No，Fragment没有setSupportActionBar，于是Google，得到：
```java
public class BaseFragment extends Fragment {

    Activity mActivity;
    AppCompatActivity mAppCompatActivity;

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        mActivity = getActivity();
        
    }

    public Toolbar initToolbar(int title) {
        AppCompatActivity mAppCompatActivity = (AppCompatActivity) mActivity;
        Toolbar toolbar = (Toolbar) mAppCompatActivity.findViewById(R.id.toolbar);
        mAppCompatActivity.setSupportActionBar(toolbar);       
        ActionBar actionBar = mAppCompatActivity.getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(false);            
        }
        return toolbar;
    }

    public Toolbar initToolbar(CharSequence title) {
        mAppCompatActivity = (AppCompatActivity) mActivity;
        Toolbar toolbar = (Toolbar) mAppCompatActivity.findViewById(toolbarId);
        mAppCompatActivity.setSupportActionBar(R.id.toolbar);
        ActionBar actionBar = mAppCompatActivity.getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(false);
        }
        return toolbar;
    }
}
```
这样就解决Fragment使用Toolbar，却抛出了一个问题：Fragment标题错乱。

# Fragment标题错乱
当出来这个问题时，我恍然大悟，Fragment里的Toolbar findViewById取的都是MainActivity，include不能共用一个ID，应该加个ID区别一下：
```js
<include
        android:id="@+id/program_toolbar"
        layout="@layout/toolbar" />
```
BaseFragment 做相应的修改：
```java
 public Toolbar initToolbar(int toolbarId, int title) {
        AppCompatActivity mAppCompatActivity = (AppCompatActivity) mActivity;
        Toolbar toolbar = (Toolbar) mAppCompatActivity.findViewById(toolbarId);
        mAppCompatActivity.setSupportActionBar(toolbar);
        ActionBar actionBar = mAppCompatActivity.getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(false);
            actionBar.setDisplayShowTitleEnabled(false);
        }
        return toolbar;
    }
```
好的，这样标题就没有错乱了，然后又来了新的“需求”：Fragment如何让Toolbar菜单生效？

# Fragment让Toolbar菜单生效
在activity中：
```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main, menu);
    return super.onCreateOptionsMenu(menu);
}
```
在Fragment中：
```java
@Override
public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
        inflater.inflate(R.menu.pictrue_list, menu);
        super.onCreateOptionsMenu(menu,inflater);
}
```
两者不同的地方在于:
1、一个有返回值（boolean类型），一个没有返回值。
2、Fragment中onCreateOptionsMenu的参数多了一个MenuInflater

想让Fragment中的onCreateOptionsMenu生效必须先调用setHasOptionsMenu方法，否则Toolbar没有菜单。
```java
public class BaseFragment extends Fragment {
    Activity mActivity;   
    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        mActivity = getActivity();
        setHasOptionsMenu(true);
    }
 }
```
setHasOptionsMenu方法源码如下：
```java
  /**
     * Report that this fragment would like to participate in populating
     * the options menu by receiving a call to {@link #onCreateOptionsMenu}
     * and related methods.
     *
     * @param hasMenu If true, the fragment has menu items to contribute.
     */
    public void setHasOptionsMenu(boolean hasMenu) {
        if (mHasMenu != hasMenu) {
            mHasMenu = hasMenu;
            if (isAdded() && !isHidden()) {
                mHost.onSupportInvalidateOptionsMenu();
            }
        }
    }
```

# 单独使用而不与ActionBar进行关联
多谢@XZoomEye提示，对于Fragment使用Toolbar思维被局限了，在[Android Toolbar](http://wuxiaolong.me/2015/11/10/toolbar/)一文中介绍了单独使用而不与ActionBar进行关联，直接使用ToolBar的inflateMenu方法，Menu的事件也是独立的，需要通过设置ToolBar的setOnMenuItemClickListener来实现，这样做就不会发生标题错乱。

# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>

