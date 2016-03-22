title: Android Design Support Library之TabLayout 
date: 2015-08-03 09:26:54
tags: [DesignSupportLibrary,TabLayout]
category: DesignSupportLibrary
---
## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/TabLayout1.gif)
<!--more-->
## 实例APK
[fir.im](https://fir.im/jinriqinggan)
[小米应用商店](http://app.mi.com/detail/30499)
[魅族应用中心](http://app.meizu.com/apps/public/detail?package_name=com.android.xiaomolongstudio.danhuaer)


## 使用方法
Gradle 中添加下面依赖
```js
 compile 'com.android.support:design:22.2.0'
```

## 使用style
```js
 <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="colorPrimary">@color/primary</item>
        <item name="colorPrimaryDark">@color/primary_dark</item>
</style>
```
## xml布局
```js
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

        <android.support.design.widget.TabLayout
            android:id="@+id/tabs"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_scrollFlags="scroll|enterAlways"
            app:tabBackground="?attr/colorPrimary"
            app:tabSelectedTextColor="@color/white"
			app:tabTextColor="?attr/cursorTextColor" />

   
    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="?attr/backgroundColor"
       />

</LinearLayout>
```
## ViewPager的适配器
```java
class ViewPagerAdapter extends FragmentPagerAdapter {
        private final List<Fragment> mFragmentList = new ArrayList<>();
        private final List<String> mFragmentTitleList = new ArrayList<>();

        public ViewPagerAdapter(FragmentManager manager) {
            super(manager);
        }

        @Override
        public Fragment getItem(int position) {
            return mFragmentList.get(position);
        }

        @Override
        public int getCount() {
            return mFragmentList.size();
        }

        public void addFrag(Fragment fragment, String title) {
            mFragmentList.add(fragment);
            mFragmentTitleList.add(title);
        }

        @Override
        public CharSequence getPageTitle(int position) {
            return mFragmentTitleList.get(position);
        }
    }
    
  private void setupViewPager(ViewPager viewPager) {
        ViewPagerAdapter adapter = new ViewPagerAdapter(getActivity().getSupportFragmentManager());


        Fragment newfragment = new TodayEmotionFragment();
        Bundle data = new Bundle();
        data.putInt("id", 0);
        newfragment.setArguments(data);
        adapter.addFrag(newfragment, getString(R.string.classical_article));

        newfragment = new TodayEmotionFragment();
        data = new Bundle();
        data.putInt("id", 3);
        newfragment.setArguments(data);
        adapter.addFrag(newfragment, "职场指南");


        newfragment = new TodayEmotionFragment();
        data = new Bundle();
        data.putInt("id", 1);
        newfragment.setArguments(data);
        adapter.addFrag(newfragment, getString(R.string.emotion_daily));

        newfragment = new TodayEmotionFragment();
        data = new Bundle();
        data.putInt("id", 2);
        newfragment.setArguments(data);
        adapter.addFrag(newfragment, getString(R.string.love_marriage));

        newfragment = new TodayEmotionFragment();
        data = new Bundle();
        data.putInt("id", 4);
        newfragment.setArguments(data);
        adapter.addFrag(newfragment, "经典语录");

        viewPager.setAdapter(adapter);

        viewPager.setOffscreenPageLimit(5);
    }
```
## 设置TabLayout
```java
    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        TabLayout tabLayout = (TabLayout) view.findViewById(R.id.tabs);

        ViewPager viewPager = (ViewPager) view.findViewById(R.id.viewPager);
        setupViewPager(viewPager);
        // 设置ViewPager的数据等
        tabLayout.setupWithViewPager(viewPager);
        tabLayout.setTabMode(TabLayout.MODE_SCROLLABLE);//适合很多tab
        //tabLayout.setTabMode(TabLayout.MODE_FIXED);//tab均分,适合少的tab
		//tabLayout.setTabGravity(TabLayout.GRAVITY_FILL);//tab均分,适合少的tab,TabLayout.GRAVITY_CENTER
    }
```
## 定义TabLayout的样式
[参考博客](http://chenfuduo.me/2015/07/30/TabLayout-of-design-support-library/)

## 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>

## 源码地址
[https://github.com/WuXiaolong/DesignSupportLibrarySample](https://github.com/WuXiaolong/DesignSupportLibrarySample)