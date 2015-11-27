title: LinearGradient实现文字一闪一闪
date: 2015-11-16 11:14:43
tags: [LinearGradient,TextView] 
category: Android
---
## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/LinearGradientTextView.gif)
<!--more-->
## 实现方法
新建LinearGradientTextView，继承TextView，重写onSizeChanged和onDraw。
```java
public class LinearGradientTextView extends TextView {
    private int mViewWidth;
    private Paint mPaint;
    private LinearGradient mLinearGradient;
    private Matrix mMatrix;
    private int mTranslate;

    public LinearGradientTextView(Context context) {
        super(context);
    }

    public LinearGradientTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public LinearGradientTextView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }


    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        if (mViewWidth == 0) {
            mViewWidth = getMeasuredWidth();
            if (mViewWidth > 0) {
                mPaint = getPaint();
                mLinearGradient = new LinearGradient(0, 0, mViewWidth, 0,
                        new int[]{Color.BLUE, Color.GRAY, Color.RED, Color.GREEN}, null, Shader.TileMode.MIRROR);
                mPaint.setShader(mLinearGradient);
                mMatrix = new Matrix();
            }
        }
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        if (mMatrix != null) {
            mTranslate += mViewWidth / 5;
            if (mTranslate > 2 * mViewWidth) {
                mTranslate = -mViewWidth;
            }
            mMatrix.setTranslate(mTranslate, 0);
            mLinearGradient.setLocalMatrix(mMatrix);
            postInvalidateDelayed(100);
        }
    }
}
```
LinearGradient有两个构造函数：
public LinearGradient(float x0, float y0, float x1, float y1, int[] colors, float[] positions,Shader.TileMode tile) 
参数:
float x0: 渐变起始点x坐标
float y0:渐变起始点y坐标
float x1:渐变结束点x坐标
float y1:渐变结束点y坐标
int[] colors:颜色 的int 数组
float[] positions: 相对位置的颜色数组,可为null,  若为null,可为null,颜色沿渐变线均匀分布
Shader.TileMode tile: 渲染器平铺模式

## xml调用
```js
<com.wuxiaolong.apksample.LinearGradientTextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:gravity="center"
        android:text="测试文字一闪一闪"
        android:textSize="30sp" />
```

## 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
