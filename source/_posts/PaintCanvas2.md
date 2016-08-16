title: Android 画笔Paint
date: 2016-08-16 17:26:15
tags:
category:
---
> 关于Android CustomView，Aige《自定义控件其实很简单》系列博客已经写的很棒了，引用他的话“很多时候你压根不需要了解太多原理，只需站在巨人的丁丁上即可”，所谓前人种树后人好乘凉，这里记录下我的实践下。

我们可以通过Paint中setter方法来为画笔设置属性：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint1.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint2.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint3.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
这些方法一一过一遍：
# set
void	set(Paint src)
为当前画笔copy一个画笔

# setARGB
void  setARGB(int a, int r, int g, int b)  
设置Paint对象颜色，参数一为alpha透明通道

# setAlpha
void  setAlpha(int a)  
设置alpha不透明度，范围为0~255

# setAntiAlias
void  setAntiAlias(boolean aa)  
是否抗锯齿

# setColor
void  setColor(int color)  
设置颜色，这里Android内部定义的有Color类包含了一些常见颜色定义

# setColorFilter 
ColorFilter setColorFilter (ColorFilter filter)
设置颜色过滤，[ColorFilter](https://developer.android.com/reference/android/graphics/ColorFilter.html)有三个子类去实现ColorMatrixColorFilter、LightingColorFilter和PorterDuffColorFilter

## ColorMatrixColorFilter
```
public class PaintCanvas extends View {
    private Paint mPaint;
    //省略构造方法
    private void init() {
        mPaint = new Paint();
        mPaint.setAntiAlias(true);
        // 生成色彩矩阵
        ColorMatrix colorMatrix = new ColorMatrix(new float[]{
                0.5F, 0, 0, 0, 0,
                0, 0.5F, 0, 0, 0,
                0, 0, 0.5F, 0, 0,
                0, 0, 0, 1, 0,
        });
        mPaint.setColorFilter(new ColorMatrixColorFilter(colorMatrix));
    }

    @Override
    protected void onDraw(Canvas canvas) {
        Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.logo);
        canvas.drawBitmap(bitmap, 0, 0, mPaint);
    }

}
```
第一行表示的R（红色）的向量，第二行表示的G（绿色）的向量，第三行表示的B（蓝色）的向量，最后一行表示A（透明度）的向量，这一顺序必须要正确不能混淆！这个矩阵不同的位置表示的RGBA值，其范围在0.0F至2.0F之间，1为保持原图的RGB?值。每一行的第五列数字表示偏移值（关于矩阵更多解释可见Aige博客）。

![](http://7q5c2h.com1.z0.glb.clouddn.com/paint4.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
这是原图效果，增加ColorMatrix，效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint5.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

## LightingColorFilter
只有一个构造方法，LightingColorFilter (int mul, int add) ，mul全称是colorMultiply意为色彩倍增，而add全称是colorAdd意为色彩添加，这两个值都是16进制?的色彩值0xAARRGGBB。
```
 // 设置颜色过滤,去掉绿色
 mPaint.setColorFilter(new LightingColorFilter(0xFFFF00FF, 0x00000000));
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint6.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

## PorterDuffColorFilter
也只有一个构造方法，PorterDuffColorFilter (int color, PorterDuff.Mode mode)，一个参数是16进制表示的颜色值，第二个参数是PorterDuff内部类Mode中的一个常量值，这个值表示混合模式。
````
// 设置颜色过滤,Color的值设为红色，模式PorterDuff.Mode.DARKEN变暗
mPaint.setColorFilter(new PorterDuffColorFilter(Color.RED, PorterDuff.Mode.DARKEN));
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint7.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
混合模式还有很多，不仅是应用于图像色彩混合，还应用于图形混合。

# setFakeBoldText
void  setFakeBoldText(boolean fakeBoldText)  
设置伪粗体文本

# setLinearText
 void  setLinearText(boolean linearText)  
设置线性文本

# setPathEffect
 PathEffect  setPathEffect(PathEffect effect)  
设置路径效果

# setRasterizer
 Rasterizer  setRasterizer(Rasterizer rasterizer) 
设置光栅化

# setShader
Shader  setShader(Shader shader)  
设置阴影，[Shader](https://developer.android.com/reference/android/graphics/Shader.html) 子类实现有BitmapShader, ComposeShader, LinearGradient, RadialGradient, SweepGradient
## BitmapShader
设置位图，构造方法：BitmapShader (Bitmap bitmap,Shader.TileMode tileX,Shader.TileMode tileY)
Shader.TileMode有三个：
CLAMP 重复最后一个颜色至最后
MIRROR 重复着色的图像水平或垂直方向已镜像方式填充会有翻转效果
REPEAT 重复着色的图像水平或垂直方向
```
public class PaintCanvas extends View {
    private Paint mPaint;
    private Context mContext;
    private Bitmap mBitmap;
    BitmapShader mBitmapShader;

    // 省略构造方法
    
    private void init() {
        mBitmap = BitmapFactory.decodeResource(mContext.getResources(), R.mipmap.logo);
        mBitmapShader = new BitmapShader(mBitmap, Shader.TileMode.REPEAT, Shader.TileMode.REPEAT);
        mPaint = new Paint();
        mPaint.setAntiAlias(true);        
    }

    @Override
    protected void onDraw(Canvas canvas) {
        mPaint.setShader(mBitmapShader);
        canvas.drawCircle(500, 550, 500, mPaint);
    }

}
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint10.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
LinearGradient——线性
RadialGrdient——光束
SweepGradient——梯度
ComposeShader——混合

* void  setTextAlign(Paint.Align align) 
设置文本对齐

* void  setTextScaleX(float scaleX) 
设置文本缩放倍数，1.0f为原始

* void  setTextSize(float textSize)
设置字体大小

* Typeface  setTypeface(Typeface typeface)
设置字体，Typeface包含了字体的类型，粗细，还有倾斜、颜色等。

* void  setUnderlineText(boolean underlineText)  
设置下划线

# Xfermode setXfermode (Xfermode xfermode)
设置图像混合模式，[Xfermode](https://developer.android.com/reference/android/graphics/Xfermode.html) 有个子类去实现PorterDuffXfermode
## PorterDuffXfermode
构造方法PorterDuffXfermode(PorterDuff.Mode mode)，参数就是上面的提到的，图形混合模式如图：

![](http://7q5c2h.com1.z0.glb.clouddn.com/PaintCanvas1.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
Dst：先画(下层)的图形；Src：后画(上层)的图形，然而被网上这张图片误导了，解释见[孙群博客](http://blog.csdn.net/iispring/article/details/50472485)，他也给了最终运行效果：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint8.jpg)
我一一运行确实是如此，这里贴出Screen代码：
```
public class PaintCanvas extends View {
    private Paint mPaint;
    private PorterDuffXfermode porterDuffXfermode;// 图形混合模式
    private Context mContext;
    private Bitmap mBitmap;
    //省略构造方法

    private void init() {
        mBitmap = BitmapFactory.decodeResource(mContext.getResources(), R.mipmap.logo);
        mPaint = new Paint();
        mPaint.setAntiAlias(true);
        // 实例化混合模式
        porterDuffXfermode = new PorterDuffXfermode(PorterDuff.Mode.SCREEN);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        int canvasWidth = canvas.getWidth();
        int canvasHeight = canvas.getHeight();
        //新建一个layer,放置在canvas默认layer的上部，产生的layer初始时是完全透明的
        int layerId = canvas.saveLayer(0, 0, canvasWidth, canvasHeight, null, Canvas.ALL_SAVE_FLAG);
        //dst是先画的图形
        canvas.drawBitmap(mBitmap, 0, 0, mPaint);
        //设置混合模式
        mPaint.setXfermode(porterDuffXfermode);
        //src是后画的图形
        mPaint.setColor(0xFFFFCC44);
        canvas.drawCircle(600, 600, 200, mPaint);
        //还原混合模式
        mPaint.setXfermode(null);
        //或canvas.restore()把这个layer绘制到canvas默认的layer上去
        canvas.restoreToCount(layerId);
    }

}
```
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint9.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)







# 鸣谢
[Android中Canvas绘图之PorterDuffXfermode使用及工作原理详解](http://blog.csdn.net/iispring/article/details/50472485)