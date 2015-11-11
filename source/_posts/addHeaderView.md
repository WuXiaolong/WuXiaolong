title: Android ListView addHeaderView
date: 2015-11-10 10:57:25
tags: [ListView,addHeaderView]
category: Android
---
最近在调试我的个人[大湿说app](http://wuxiaolong.github.io/2015/11/09/dashishuo/)，发现在4.2系统下直接崩溃了，看log，原来是ListView addHeaderView和setAdapter方法调用顺利不同导致的错误，这里做下记录。
<!--more-->
## 正确调用
```java
 mListView.addHeaderView(mHeadView);
 mListView.setAdapter(mListViewAdapter);
```
## 究其原因
Android-18（4.3）的addHeaderView源码：
```java
public void addHeaderView(View v, Object data, boolean isSelectable) {
        final FixedViewInfo info = new FixedViewInfo();
        info.view = v;
        info.data = data;
        info.isSelectable = isSelectable;
        mHeaderViewInfos.add(info);
 
        // Wrap the adapter if it wasn't already wrapped.
        if (mAdapter != null) {
            if (!(mAdapter instanceof HeaderViewListAdapter)) {
                mAdapter = new HeaderViewListAdapter(mHeaderViewInfos, mFooterViewInfos, mAdapter);
            }
 
            // In the case of re-adding a header view, or adding one later on,
            // we need to notify the observer.
            if (mDataSetObserver != null) {
                mDataSetObserver.onChanged();
            }
        }
}
```
Android-17（4.2）的addHeaderView的源码：
```java
public void addHeaderView(View v, Object data, boolean isSelectable) {
 
        if (mAdapter != null && ! (mAdapter instanceof HeaderViewListAdapter)) {
            throw new IllegalStateException(
                    "Cannot add header view to list -- setAdapter has already been called.");
        }
 
        FixedViewInfo info = new FixedViewInfo();
        info.view = v;
        info.data = data;
        info.isSelectable = isSelectable;
        mHeaderViewInfos.add(info);
 
        // in the case of re-adding a header view, or adding one later on,
        // we need to notify the observer
        if (mAdapter != null && mDataSetObserver != null) {
            mDataSetObserver.onChanged();
        }
}
```
在17版本中，只要adapter不为空的话，那就直接会抛出异常，而这个异常恰好就是我们文章开头说到的异常。在18版本中，如果adapter不为空的话，则会新建一个adapter，这个adapter会包含了headerview和footerview以及我们传进来的原来的adapter。这是在18版本以后做的一个处理。
以上源码分析来自:[从源码上分析ListView的addHeaderView和setAdapter的调用顺序](http://blog.csdn.net/wuzhipeng1991/article/details/39023883)

## 大湿说
![](http://7q5c2h.com1.z0.glb.clouddn.com/dashishuo_meizu.png)
魅族应用市场累计下载`4.4万+`，我可没有做过什么推广哦，精品APP，值得下载！
## 二维码下载
![](http://7q5c2h.com1.z0.glb.clouddn.com/dashishuoDownload.png)

## 应用市场
[魅族应用市场](http://app.meizu.com/apps/public/detail?package_name=com.xiaomolong.ufosay)
[小米应用市场](http://app.mi.com/detail/65621)