title: Android 画笔Paint
date: 2015-12-16 17:26:15
tags: Paint
category: CustomView
---
> 关于Android CustomView，Aige《自定义控件其实很简单》系列博客已经写的很棒了，引用他的话“很多时候你压根不需要了解太多原理，只需站在巨人的丁丁上即可”，所谓前人种树后人好乘凉，这里记录下我的实践结果。

我们可以通过Paint中setter方法来为画笔设置属性：
<!--more-->
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

# setDither
```
void setDither(boolean dither)
```
设定是否使用图像抖动处理，会使绘制出来的图片颜色更加平滑和饱满，图像更加清晰

# setElegantTextHeight
```
void setElegantTextHeight(boolean elegant)
```
# setFakeBoldText
```
void setFakeBoldText(boolean fakeBoldText)
```
设置伪粗体文本

# setFilterBitmap
```
void setFilterBitmap(boolean filter)
```
是否过滤位图

# setFontFeatureSettings
```
void setFontFeatureSettings(String settings)
```
设置字体功能

# setHinting
```
void setHinting (int mode)
```
设置暗示模式，HINTING_OFF 或 HINTING_ON

# setLetterSpacing
```
void setLetterSpacing (float letterSpacing)
```
设置文本字母间距，默认0，负值收紧文本

# setLinearText
 void  setLinearText(boolean linearText)  
设置线性文本

# setMaskFilter
```
MaskFilter setMaskFilter (MaskFilter maskfilter)
```

# setPathEffect
 PathEffect  setPathEffect(PathEffect effect)  
设置路径效果，PathEffect有6个子类实现ComposePathEffect, CornerPathEffect, DashPathEffect, DiscretePathEffect, PathDashPathEffect, SumPathEffect
具体代码：
```
public class PaintCanvas extends View {
    private Paint mPaint;   
    // 路径对象
    private Path mPath;
    private PathEffect[] pathEffects = new PathEffect[7];
    private float mPhase=5;

   //省略构造方法


    private void init() {       
        mPaint = new Paint();
        mPaint.setStyle(Paint.Style.STROKE);
        mPaint.setStrokeWidth(5);
        mPaint.setAntiAlias(true);
        initPath();       

    }

    private void initPath() {
        // 实例化路径
        mPath = new Path();
        // 定义路径的起点
        mPath.moveTo(10, 50);

        // 定义路径的各个点
        for (int i = 0; i <= 30; i++) {
            mPath.lineTo(i * 35, (float) (Math.random() * 100));
        }
        //什么都不处理
        pathEffects[0] = null;
        //参数1：线段之间的圆滑程度
        pathEffects[1] = new CornerPathEffect(10);
        //参数1：间隔线条长度(length>=2)，如float[] {20, 10}的偶数参数20定义了我们第一条实线的长度，
        //而奇数参数10则表示第一条虚线的长度，后面不再有数据则重复第一个数以此往复循环；参数2：虚实线间距
        pathEffects[2] = new DashPathEffect(new float[]{20, 10}, mPhase);
        //参数1:值越小杂点越密集；参数2:杂点突出的大小，值越大突出的距离越大
        pathEffects[3] = new DiscretePathEffect(5.0f, 10.0f);
        Path path = new Path();
        path.addRect(0, 0, 8, 8, Path.Direction.CCW);
        //定义路径虚线的样式,参数1：path；参数2：实线的长度；参数3：虚实线间距
        pathEffects[4] = new PathDashPathEffect(path, 20, mPhase, PathDashPathEffect.Style.ROTATE);
        pathEffects[5] = new ComposePathEffect(pathEffects[2], pathEffects[4]);
        //ComposePathEffect和SumPathEffect都可以用来组合两种路径效果,具体区别（不知道如何描述）小伙伴们自己试试
        pathEffects[6] = new SumPathEffect(pathEffects[4], pathEffects[3]);
    }

    @Override
    protected void onDraw(Canvas canvas) {      
         /*
         * 绘制路径
         */
        for (int i = 0; i < pathEffects.length; i++) {
            mPaint.setPathEffect(pathEffects[i]);
            canvas.drawPath(mPath, mPaint);
            // 每绘制一条将画布向下平移250个像素
            canvas.translate(0, 250);
        }
    }

}
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint17.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

# setRasterizer
 Rasterizer  setRasterizer(Rasterizer rasterizer) 
设置光栅化

# setShader
Shader  setShader(Shader shader)  
设置阴影，[Shader](https://developer.android.com/reference/android/graphics/Shader.html) 子类实现有BitmapShader, ComposeShader, LinearGradient, RadialGradient, SweepGradient
## BitmapShader
对图形进行渲染，构造方法：
```
BitmapShader (Bitmap bitmap,Shader.TileMode tileX,Shader.TileMode tileY)
```
tileX、tileY参数Shader.TileMode有三个：
CLAMP 重复最后一个颜色至最后
MIRROR 重复着色的图像水平或垂直方向已镜像方式填充会有翻转效果
REPEAT 重复着色的图像水平或垂直方向

设置tileX、tileY为Shader.TileMode.CLAMP
```
public class PaintCanvas extends View {
    private Paint mPaint;
    private Context mContext;
    private Bitmap mBitmap;
    private BitmapShader mShader;

    // 省略构造方法
    
    private void init() {
        mBitmap = BitmapFactory.decodeResource(mContext.getResources(), R.mipmap.logo);
        mShader= new BitmapShader(mBitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
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
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint12.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

设置tileX、tileY为Shader.TileMode.MIRROR
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint11.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

设置tileX、tileY为Shader.TileMode.REPEAT 
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint10.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)


## LinearGradient
设置线性渐变效果，有两个构造函数
```
//坐标(x0,y0)渐变直线的起点，坐标(x1,y1)渐变直线的终点，color0和color1分别表示了渐变的起始颜色和终止颜色，TileMode也有CLAMP 、REPEAT 和 MIRROR三个取值
LinearGradient(float x0, float y0, float x1, float y1, int color0, int color1, Shader.TileMode tile)
LinearGradient(float x0, float y0, float x1, float y1, int[] colors, float[] positions,Shader.TileMode tile)

```
来个例子：
```
public class PaintCanvas extends View {
    private Paint mPaint;
    private Context mContext;
    private Bitmap mBitmap;
    private BitmapShader mBitmapShader;

    // 省略构造方法
    
    private void init() {
        mShader = new LinearGradient(0, 0, 500, 500, Color.BLUE, Color.GREEN,Shader.TileMode.CLAMP);
        mPaint = new Paint();
        mPaint.setAntiAlias(true);        
    }

    @Override
    protected void onDraw(Canvas canvas) {
        mPaint.setShader(mBitmapShader);
        canvas.drawCircle(500, 550, 400, mPaint);
    }

}
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint13.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
设置REPEAT 和 MIRROR就不贴图片了，小伙伴们可以自己试试看看效果。

## RadialGrdient
设置光束从中心向四周发散的辐射渐变效果，构造方法：
```
//坐标(centerX,centerY)中心点坐标，radius圆的半径，centerColor中心颜色，edgeColor圆的轮廓颜色，颜色逐渐从centerColor渐变到edgeColor，TileMode也有CLAMP 、REPEAT 和 MIRROR三个取值
RadialGradient(float centerX, float centerY, float radius, int centerColor, int edgeColor, Shader.TileMode tileMode)
RadialGradient(float centerX, float centerY, float radius, int[] colors, float[] stops, Shader.TileMode tileMode)
```
例子：
```
public class PaintCanvas extends View {
    private Paint mPaint;
    private Context mContext;
    private Bitmap mBitmap;
    private BitmapShader mBitmapShader;

    // 省略构造方法
    
    private void init() {
        mShader = new RadialGradient(500, 500, 400, Color.BLUE, Color.GREEN, Shader.TileMode.CLAMP);
        mPaint = new Paint();
        mPaint.setAntiAlias(true);        
    }

    @Override
    protected void onDraw(Canvas canvas) {
        mPaint.setShader(mBitmapShader);
        canvas.drawCircle(500, 550, 400, mPaint);
    }

}
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint14.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
同样设置REPEAT 和 MIRROR就不贴图片了。

## SweepGradient
设置绕着某中心点进行360度旋转渐变效果，构造方法：
```
//坐标(cx,cy)决定了中心点的位置，会绕着该中心点进行360度旋转。color0表示的是起点的颜色，color1表示的是终点的颜色
SweepGradient(float cx, float cy, int color0, int color1)
//坐标(cx,cy)决定了中心点的位置,colors颜色数组,position取值范围为[0,1]，0和1都表示3点钟位置，0.25表示6点钟位置，0.5表示9点钟位置，0.75表示12点钟位置，诸如此类
SweepGradient(float cx, float cy, int[] colors, float[] positions)
```
例子：
```
public class PaintCanvas extends View {
    private Paint mPaint;
    private Context mContext;
    private Bitmap mBitmap;
    private BitmapShader mBitmapShader;

    // 省略构造方法
    
    private void init() {
        mShader = new SweepGradient(500, 500, Color.BLUE, Color.GREEN);
        mPaint = new Paint();
        mPaint.setAntiAlias(true);        
    }

    @Override
    protected void onDraw(Canvas canvas) {
        mPaint.setShader(mBitmapShader);
        canvas.drawCircle(500, 550, 400, mPaint);
    }

}
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint15.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

## ComposeShader
混合，有两个构造函数
```
//shaderA对应下层图形，shaderB对应上层图形
ComposeShader(Shader shaderA, Shader shaderB, Xfermode mode)
ComposeShader(Shader shaderA, Shader shaderB, PorterDuff.Mode mode)
```
例子：
```
public class PaintCanvas extends View {
    private Paint mPaint;
    private Context mContext;
    private Bitmap mBitmap;
    private Shader bitmapShader, linearGradient, composeShader;

    // 省略构造方法
    
    private void init() {
        mBitmap = BitmapFactory.decodeResource(mContext.getResources(), R.mipmap.logo);
        bitmapShader = new BitmapShader(mBitmap, Shader.TileMode.MIRROR, Shader.TileMode.MIRROR);
        linearGradient = new LinearGradient(0, 0, mBitmap.getWidth(), mBitmap.getHeight(), Color.BLUE, Color.GREEN, Shader.TileMode.CLAMP);
        //bitmapShader对应下层图形，linearGradient对应上层图形，像素颜色混合采用MULTIPLY模式
        composeShader = new ComposeShader(bitmapShader, linearGradient, PorterDuff.Mode.MULTIPLY);
        mPaint = new Paint();
        mPaint.setAntiAlias(true);        
    }

    @Override
    protected void onDraw(Canvas canvas) {
        mPaint.setShader(mBitmapShader);
        canvas.drawCircle(500, 550, 400, mPaint);
    }
}
```
效果如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/paint16.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

# setShadowLayer
void	setShadowLayer(float radius, float dx, float dy, int shadowColor)
图形添加一个阴影层效果

# setTextAlign
void  setTextAlign(Paint.Align align) 
设置文本对齐

# setTextScaleX
void  setTextScaleX(float scaleX) 
设置文本缩放倍数，1.0f为原始

# setTextSize
 void  setTextSize(float textSize)
设置字体大小

# setTypeface 
Typeface  setTypeface(Typeface typeface)
设置字体，Typeface包含了字体的类型，粗细，还有倾斜、颜色等。
```
mPaint.setTypeface(Typeface.SANS_SERIF);
```
# setUnderlineText
void  setUnderlineText(boolean underlineText)  
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
[自定义控件其实很简单1/4](http://blog.csdn.net/aigestudio/article/details/41447349)
[Android中Canvas绘图之PorterDuffXfermode使用及工作原理详解](http://blog.csdn.net/iispring/article/details/50472485)