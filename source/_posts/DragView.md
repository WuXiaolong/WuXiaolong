title: Android之滑动view
date: 2015-11-20 13:11:53
tags: DragView
category: CustomView
---
# 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/DragView.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

<!--more-->
# 获取坐标值方法
![](http://7q5c2h.com1.z0.glb.clouddn.com/DragView.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
如图，要实现滑动效果，需要先了解以下方法。
* View提供获取坐标方法
getTop():获取View自身顶边到其父布局顶边的距离
getLeft():获取View自身左边到其父布局左边的距离
getRight():获取View自身右边到其父布局右边的距离
getBottom():获取View自身底边到其父布局底边的距离

* MotionEvent提供的方法
getX():获取点击点距离自身控件左边距离，即视图坐标
getY():获取点击点距离自身控件顶边距离，即视图坐标
getRawX():获取点击点距离整个屏幕左边距离，即绝对坐标
getRawY():获取点击点距离整个屏幕顶边距离，即绝对坐标

# 实现DragView
## 方法一
```java
public class DragView extends View {
    private int x, y;

    public DragView(Context context) {
        super(context);

    }

    public DragView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public DragView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }


    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int getX = (int) event.getX();
        int getY = (int) event.getY();
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                // 记录触摸点坐标
                x = (int) event.getX();
                y = (int) event.getY();
                break;
            case MotionEvent.ACTION_MOVE:
                //计算偏移量
                int offsetX = getX - x;
                int offsetY = getY - y;
                //在当前left、top、right、bottom的基础上加上偏移量
                layout(getLeft() + offsetX,
                        getTop() + offsetY,
                        getRight() + offsetX,
                        getBottom() + offsetY);

                break;
        }
        return true;
    }

}
```
## 方法二
```java
public class DragView extends View {
    private int x, y;
    //这里省略构造方法
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int getX = (int) event.getX();
        int getY = (int) event.getY();
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                // 记录触摸点坐标
                x = (int) event.getX();
                y = (int) event.getY();
                break;
            case MotionEvent.ACTION_MOVE:
                //计算偏移量
                int offsetX = getX - x;
                int offsetY = getY - y;
                offsetLeftAndRight(offsetX);//同时对left和right偏移
                offsetTopAndBottom(offsetY);//同时对top和bottom偏移
                break;
        }
        return true;
    }
}
```
## 方法三
```java
```java
public class DragView extends View {
    private int x, y;
    //这里省略构造方法
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int getX = (int) event.getX();
        int getY = (int) event.getY();
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                // 记录触摸点坐标
                x = (int) event.getX();
                y = (int) event.getY();
                break;
            case MotionEvent.ACTION_MOVE:
                //计算偏移量
                int offsetX = getX - x;
                int offsetY = getY - y;
                ViewGroup.MarginLayoutParams marginLayoutParams = 
                (ViewGroup.MarginLayoutParams) getLayoutParams();
                marginLayoutParams.leftMargin = getLeft() + offsetX;
                marginLayoutParams.topMargin = getTop() + offsetY;
                setLayoutParams(marginLayoutParams);
                break;
        }
        return true;
    }
}
```
## 方法四
```java
public class DragView extends View {
    private int x, y;
    private Scroller mScroller;

    public DragView(Context context) {
        super(context);
        initScroller(context);
    }

    public DragView(Context context, AttributeSet attrs) {
        super(context, attrs);
        initScroller(context);
    }

    public DragView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initScroller(context);
    }

    private void initScroller(Context context) {
        setBackgroundColor(Color.RED);
        mScroller = new Scroller(context);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int getRawX = (int) event.getRawX();
        int getRawY = (int) event.getRawY();
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                // 记录触摸点坐标
                x = (int) event.getRawX();
                y = (int) event.getRawY();
                break;
            case MotionEvent.ACTION_MOVE:
                //计算偏移量
                int offsetX = getRawX - x;
                int offsetY = getRawY - y;
                ((View) getParent()).scrollBy(-offsetX, -offsetY);
                //重新设置初始坐标
                x = getRawX;
                y = getRawY;
                break;
            case MotionEvent.ACTION_UP:
                /**
                 * 实现拖动回弹回去，需配合方法一、二、四中任一方法
                 */
                View viewGroup = (View) getParent();
                mScroller.startScroll(viewGroup.getScrollX(),
                        viewGroup.getScrollY(),
                        -viewGroup.getScrollX(),
                        -viewGroup.getScrollY()
                );
                invalidate();
                break;
        }
        return true;
    }

    @Override
    public void computeScroll() {
        super.computeScroll();
        //判断Scroller是否执行完毕
        if (mScroller.computeScrollOffset()) {
            ((View) getParent()).scrollTo(
                    mScroller.getCurrX(),
                    mScroller.getCurrY());
            invalidate();
        }
    }
}
```
xml调用
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.wuxiaolong.apksample.DragView
        android:layout_width="100dp"
        android:layout_height="100dp" 
        android:background="@android:color/holo_red_light"/>
</LinearLayout>

```
## 方法五
[属性动画](http://wuxiaolong.me/2015/11/23/Animator/)

## 方法六
[ViewDragHelper](http://wuxiaolong.me/2015/12/04/ViewDragHelper/)

以上内容来自《Android群英传》

# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
