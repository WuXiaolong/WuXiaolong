title: PullLoadMoreRecyclerView
date: 2015-07-24 07:56:29 #发表日期，一般不改动
categories: Android
tags: [Android,RecyclerView]
---


# PullLoadMoreRecyclerView
RecyclerView实现下拉刷新和上拉加载更多以及瀑布流效果


<!--more-->

# 效果预览
![](https://github.com/WuXiaolong/PullLoadMoreRecyclerView/raw/master/screenshots/screenshots.gif)

# 实例APP
[魅族应用商店](http://app.meizu.com/apps/public/detail?package_name=com.xiaomolong.ufosay)

# 使用方法
build.gradle文件
```java
dependencies {
  compile 'com.wuxiaolong.pullloadmorerecyclerview:library:1.0.1'
}
```
设置线性布局
```java
  mPullLoadMoreRecyclerView = (PullLoadMoreRecyclerView) view.findViewById(R.id.pullLoadMoreRecyclerView);
  mPullLoadMoreRecyclerView.setLinearLayout();
```
设置网格布局
```java
 mPullLoadMoreRecyclerView.setGridLayout(2);//参数为列数
```
设置交错网格布局，即瀑布流效果
```java
 mPullLoadMoreRecyclerView.setStaggeredGridLayout(2);//参数为列数
```


# 源码地址
[PullLoadMoreRecyclerView](https://github.com/WuXiaolong/PullLoadMoreRecyclerView)

# 更多交流
### Android技术交流群
③群：370527306<a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>






