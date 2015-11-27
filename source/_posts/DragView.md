title: Android之滑动view
date: 2015-11-20 13:11:53
tags: DragView
category: Android
---
## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/DragView.gif)

<!--more-->
## 获取坐标值方法
![](http://7q5c2h.com1.z0.glb.clouddn.com/DragView.png)
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

## 实现DragView
```java
public class DragView extends View {
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

    private int lastX, lastY;

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int rawX = (int) event.getRawX();
        int rawY = (int) event.getRawY();
        /**
         * 方法3
         */
//        int x = (int) event.getX();
//        int y = (int) event.getY();
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                // 记录触摸点坐标
                lastX = rawX;
                lastY = rawY;
                /**
                 * 方法3
                 */
//                lastX = (int) event.getX();
//                lastY = (int) event.getY();
                break;
            case MotionEvent.ACTION_MOVE:
                //计算偏移量
                int offsetX = rawX - lastX;
                int offsetY = rawY - lastY;
                /**
                 * 方法1
                 */
                //在当前left、top、right、bottom的基础上加上偏移量
                //layout(getLeft() + offsetX,
                //getTop() + offsetY,
                //getRight() + offsetX,
                //getBottom() + offsetY);
                /**
                 * 方法2
                 */
                //offsetLeftAndRight(offsetX);//同时对left和right偏移
                //offsetTopAndBottom(offsetY);//同时对top和bottom偏移

                /**
                 *方法3
                 */
                ((View) getParent()).scrollBy(-offsetX, -offsetY);
                //重新设置初始坐标,方法3不需要
                lastX = rawX;
                lastY = rawY;
                /**
                 * 方法4
                 */
//                int offsetX = x - lastX;
//                int offsetY = y - lastY;
//                ViewGroup.MarginLayoutParams marginLayoutParams = (ViewGroup.MarginLayoutParams) getLayoutParams();
//                //LinearLayout.LayoutParams layoutParams = (LinearLayout.LayoutParams) getLayoutParams();
//                marginLayoutParams.leftMargin = getLeft() + offsetX;
//                marginLayoutParams.topMargin = getTop() + offsetY;
//                setLayoutParams(marginLayoutParams);
                break;
            case MotionEvent.ACTION_UP:
                /**
                 * 实现拖动又返回，需配合方法1-3中任一方法
                 */
//                View viewGroup = (View) getParent();
//                mScroller.startScroll(viewGroup.getScrollX(),
//                        viewGroup.getScrollY(),
//                        -viewGroup.getScrollX(),
//                        -viewGroup.getScrollY()
//                );
//                invalidate();
                break;
        }
        return true;
    }

    @Override
    public void computeScroll() {
        super.computeScroll();
        //判断Scroller是否执行完毕
        if (mScroller.computeScrollOffset()) {
            ((View) getParent()).scrollTo(mScroller.getCurrX(),
                    mScroller.getCurrY());
            //
            invalidate();
        }
    }
}

```
xml调用
```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.wuxiaolong.apksample.DragView
        android:layout_width="100dp"
        android:layout_height="100dp" />
</LinearLayout>

```

## 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
