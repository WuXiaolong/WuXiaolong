title: Android事件传递机制分析
date: 2015-12-19 16:17:02
tags: MotionEvent
category: Android
---
# 准备阶段
为了方便了解整个事件传递的机制，我们设计一个场景：
![](http://7q5c2h.com1.z0.glb.clouddn.com/MotionEvent.png)
角色：
一个经理：MotionEventViewGroupA，最外层的ViewGroupA；
一个组长：MotionEventViewGroupB，中间的ViewGroupA；
一个你：MotionEventViewC，最底层的码农。
模拟：
经理分派任务，下属处理这个任务的过程。
<!--more-->
MotionEventViewGroupA
```java
public class MotionEventViewGroupA extends LinearLayout {
    public MotionEventViewGroupA(Context context) {
        super(context);
    }

    public MotionEventViewGroupA(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MotionEventViewGroupA(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

 @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        Log.e("wxl", "MotionEventViewGroupA dispatchTouchEventA");
        return super.dispatchTouchEvent(ev);
    }
    
    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        Log.e("wxl", "MotionEventViewGroupA onInterceptTouchEventA");
        return super.onInterceptTouchEvent(ev);
    }   

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        Log.e("wxl", "MotionEventViewGroupA onTouchEventA");
        return super.onTouchEvent(event);
    }

}
```
MotionEventViewGroupB 
```java
public class MotionEventViewGroupB extends LinearLayout {

    public MotionEventViewGroupB(Context context) {
        super(context);
    }

    public MotionEventViewGroupB(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MotionEventViewGroupB(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

 @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        Log.e("wxl", "MotionEventViewGroupB dispatchTouchEventB");
        return super.dispatchTouchEvent(ev);
    }
    
    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        Log.e("wxl", "MotionEventViewGroupB onInterceptTouchEventB");
        return super.onInterceptTouchEvent(ev);
    } 

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        Log.e("wxl", "MotionEventViewGroupB onTouchEventB");
        return super.onTouchEvent(event);
    }
}
```
MotionEventViewC 
```java
public class MotionEventViewC extends View {
    public MotionEventViewC(Context context) {
        super(context);
    }

    public MotionEventViewC(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MotionEventViewC(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        Log.e("wxl", "MotionEventViewC dispatchTouchEventC");
        return super.dispatchTouchEvent(ev);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        Log.e("wxl", "MotionEventViewC onTouchEventC");
        return super.onTouchEvent(event);
    }
}

```
xml
```js
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.wuxiaolong.apksample.MotionEventActivity">

    <com.wuxiaolong.apksample.MotionEventViewGroupA
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center"
        android:background="@android:color/holo_orange_dark"
        android:gravity="center">

        <com.wuxiaolong.apksample.MotionEventViewGroupB
            android:layout_width="300dp"
            android:layout_height="300dp"
            android:background="@android:color/holo_purple"
            android:gravity="center">

            <com.wuxiaolong.apksample.MotionEventViewC
                android:layout_width="200dp"
                android:layout_height="200dp"
                android:background="@android:color/holo_green_dark"
                android:gravity="center" />
        </com.wuxiaolong.apksample.MotionEventViewGroupB>
    </com.wuxiaolong.apksample.MotionEventViewGroupA>
</FrameLayout>

```
ViewGroup级别比较高，比View多一个onInterceptTouchEvent（拦截）。

# 情景分析
## 不做任何修改，点击MotionEventViewC 
```js
 MotionEventViewGroupA dispatchTouchEventA
 MotionEventViewGroupA onInterceptTouchEventA
 MotionEventViewGroupB dispatchTouchEventB
 MotionEventViewGroupB onInterceptTouchEventB
 MotionEventViewC dispatchTouchEventC
 MotionEventViewC onTouchEventC
 MotionEventViewGroupB onTouchEventB
 MotionEventViewGroupA onTouchEventA
```
log信息看出，正常情况，事件传递顺序：
经理  -->  组长  -->  你，先执行dispatchTouchEvent（分发），再执行onInterceptTouchEvent（拦截）

事件处理顺序：
你  -->  组长  -->  经理，事件处理都是执行onTouchEvent（处理）。

事件传递返回值：true，拦截，交给自己的onTouchEvent处理；false，不拦截，传给下属。

事件处理返回值：true，自己搞定，不用上报上司；false，上报上司处理。

初始返回都是false。

事件传递，dispatchTouchEvent一般不太会改写，只关心onInterceptTouchEvent。

## 经理觉得这个任务太简单，自己处理
即MotionEventViewGroupA里onInterceptTouchEvent返回true，我们看下log信息：
```js
 MotionEventViewGroupA dispatchTouchEventA
 MotionEventViewGroupA onInterceptTouchEventA
 MotionEventViewGroupA onTouchEventA
```
经理一个人干完了，没下面的人什么事了。

## 组长觉得这个任务太简单，自己处理
即MotionEventViewGroupB里onInterceptTouchEvent返回true，我们看下log信息：
```js 
 MotionEventViewGroupA dispatchTouchEventA
 MotionEventViewGroupA onInterceptTouchEventA
 MotionEventViewGroupB dispatchTouchEventB
 MotionEventViewGroupB onInterceptTouchEventB
 MotionEventViewGroupB onTouchEventB
 MotionEventViewGroupA onTouchEventA
```
组长干完了活，没你啥事了。以上对事件的分发、拦截的分析。

## 你迫于压力，辞职不干了，任务闲置
即MotionEventViewC里onTouchEvent返回true，我们看下log信息：
```js
 MotionEventViewGroupA dispatchTouchEventA
 MotionEventViewGroupA onInterceptTouchEventA
 MotionEventViewGroupB dispatchTouchEventB
 MotionEventViewGroupB onInterceptTouchEventB
 MotionEventViewC dispatchTouchEventC
 MotionEventViewC onTouchEventC
```
事件处理到你这里就结束了。

## 组长觉得你任务完成太烂，不敢上报
即MotionEventViewB里onTouchEvent返回true，我们看下log信息：
```js
MotionEventViewGroupA dispatchTouchEventA
MotionEventViewGroupA onInterceptTouchEventA
MotionEventViewGroupB dispatchTouchEventB
MotionEventViewGroupB onInterceptTouchEventB
MotionEventViewC dispatchTouchEventC
MotionEventViewC onTouchEventC
MotionEventViewGroupB onTouchEventB
```
事件处理经过你和组长，到组长那里就结束了。

`是不是这样的分析让你对事件的传递不再是一头雾水了，大赞医生《Android群英传》。`

# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>


