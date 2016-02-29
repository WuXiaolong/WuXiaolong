title: Android CustomView
date: 2016-01-01 15:40:16
tags: CustomView
category: Android 
---
# View工作流程
View工作流程主要指measure、layout、draw这三个流程，即测量、布局和绘制，其中measure确定View的测量的宽/高，layout确定View的最终宽/高和四个顶点的位置，draw将View绘制到屏幕上。
<!--more-->
## measure
为了更好理解measure过程，先了解MeasureSpec，MeasureSpec代表一个32位int值，高2位代表SpecMode，低30位代表SpecSize（这句话不知道几个意思）。SpecMode指的测量模式，SpecSize指在某种测量模式下规格大小。
SpecMode有三种：
* EXACTLY
精确值模式，父容器已经检测出View所需要的精确大小，这时候View的最终大小就是SpecSize所指定的值。它对应代码LayoutParams（控件layout_width属性和layout_height属性）中match_parent和具体数值。
* AT_MOST
最大值模式，父容器指定了一个可用大小值，只要不超过父容器允许最大尺寸即可。它对应代码LayoutParams（控件layout_width属性和layout_height属性）中wrap_content。

* UNSPECIFIED
父容器不对View有任何限制，要多大给多大，这种情况一般用于系统内部，表示一种测量的状态。

自定义控件不重写onMeasure()，View类默认只支持EXACTLY模式，如果让View支持wrap_content属性，必须重写onMeasure()来指定wrap_content大小。代码如下：
```java
 int mWidth = 400, mHeight = 200;
 @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);
        int widthSpecSize = MeasureSpec.getSize(widthMeasureSpec);
        int heightSpecMode = MeasureSpec.getMode(heightMeasureSpec);
        int heightSpecSize = MeasureSpec.getSize(heightMeasureSpec);
        if (widthSpecMode == MeasureSpec.AT_MOST && heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(Math.min(mWidth, widthSpecSize), Math.min(mHeight, heightSpecSize));
        } else if (widthSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(Math.min(mWidth, widthSpecSize), heightSpecSize);
        } else if (heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(widthSpecSize, Math.min(mHeight, heightSpecSize));
        }
    }
```
## layout
layout的作用是ViewGroup用来确定子元素的位置，当ViewGroup的位置被确定后，他在onLayout会遍历所有子元素并调用其layout方法。
layout方法大致流程：首先通过setFrame设定View四个顶点位置，View四个顶点一旦确定，那么它在父容器中位置也就确定了。
## draw
重写onDraw()方法，并在Canvas对象上绘制所需要的图形，关于Canvas详见博客：[Android之画笔Paint和画布Canvas及实例练习圆角、刮刮卡、圆形头像、倒影效果](http://wuxiaolong.me/2015/12/06/PaintCanvas/)

# 自定义attrs属性
## attrs.xml
values目录下创建attrs.xml
```js 
<resources>
    <!--<declare-styleable>声明自定义属性-->
    <declare-styleable name="CircleView">
	    <!--<attr>声明具体自定义属性-->
        <attr name="circle_color" format="color" />
    </declare-styleable>
</resources>
```
这里format的color指的颜色。其他：reference指资源ID；dimension指尺寸；string、integer、boolean指基本数据类型。也可以用“|”来分隔不同的属性。
## 完整代码
```java
public class CustomView extends View {
    int mWidth = 500, mHeight = 450;
    int mCircleColor;
    Paint mPaint;

    public CustomView(Context context) {
        super(context);
    }

    public CustomView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(context, attrs);
    }

    public CustomView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context, attrs);
    }

    private void init(Context context, AttributeSet attrs) {
        TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.CircleView);
        mCircleColor = typedArray.getColor(R.styleable.CircleView_circle_color, Color.RED);//如果没有指定颜色，默认红色
        typedArray.recycle();
        mPaint = new Paint();
        mPaint.setColor(mCircleColor);
        mPaint.setAntiAlias(true);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);
        int widthSpecSize = MeasureSpec.getSize(widthMeasureSpec);
        int heightSpecMode = MeasureSpec.getMode(heightMeasureSpec);
        int heightSpecSize = MeasureSpec.getSize(heightMeasureSpec);
        //view支持wrap_content
        if (widthSpecMode == MeasureSpec.AT_MOST && heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(Math.min(mWidth, widthSpecSize), Math.min(mHeight, heightSpecSize));
        } else if (widthSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(Math.min(mWidth, widthSpecSize), heightSpecSize);
        } else if (heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(widthSpecSize, Math.min(mHeight, heightSpecSize));
        }
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        //支持padding，不然padding属性无效
        int paddingLeft = getPaddingLeft();
        int paddingTop = getPaddingTop();
        int paddingRight = getPaddingRight();
        int paddingBottom = getPaddingBottom();
        int width = getWidth() - paddingLeft - paddingRight;
        int height = getHeight() - paddingTop - paddingBottom;
        int radius = Math.min(width, height) / 2;
        canvas.drawCircle(paddingLeft + width / 2, paddingTop + height / 2, radius, mPaint);
    }
}
```
## xml引用
```js
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:circle="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.wuxiaolong.apksample.CustomViewActivity">

    <com.wuxiaolong.apksample.CustomView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:paddingTop="10dp"
        android:background="@android:color/darker_gray"
        circle:circle_color="@android:color/holo_blue_light" />
</RelativeLayout>
```
布局文件添加schemas声明： xmlns:circle="http://schemas.android.com/apk/res-auto" ，这里circle是自定义前缀，名字随便取。但与下面CustomView中自定义属性circle:circle_color=""前缀一致。另外也可以声明 xmlns:circle="http://schemas.android.com/apk/res/包名" ，效果是一样的。
感谢《Android群英传》《Android开发艺术探索》。
# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
