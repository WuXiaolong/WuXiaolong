title: Android之画笔Paint和画布Canvas及实例练习圆角、刮刮卡、圆形头像、倒影效果
date: 2015-12-06 10:45:38
tags: [Paint,Canvas,PorterDuffXfermode,Shader]
category: Android
---
> 先了解画笔Paint和画布Canvas一些基本方法，然后学习画笔特效处理的高级属性PorterDuffXfermode、Shader、PathEffect，并实现圆角、刮刮卡、圆形头像、倒影效果

<!--more-->

# Paint

* void  setARGB(int a, int r, int g, int b)  
设置Paint对象颜色，参数一为alpha透明通道

* void  setAlpha(int a)  
设置alpha不透明度，范围为0~255

* void  setAntiAlias(boolean aa)  
是否抗锯齿

* void  setColor(int color)  
设置颜色，这里Android内部定义的有Color类包含了一些常见颜色定义

* void  setFakeBoldText(boolean fakeBoldText)  
设置伪粗体文本

* void  setLinearText(boolean linearText)  
设置线性文本

* PathEffect  setPathEffect(PathEffect effect)  
设置路径效果

* Rasterizer  setRasterizer(Rasterizer rasterizer) 
设置光栅化

* Shader  setShader(Shader shader)  
设置阴影

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

# Canvas

* void drawRect(RectF rect, Paint paint) 
绘制矩形，参数一为RectF一个区域

* void drawRoundRect(@NonNull RectF rect, float rx, float ry, @NonNull Paint paint)
绘制圆角矩形

* void drawPath(Path path, Paint paint) 
绘制一个路径，参数一为Path路径对象

* void  drawBitmap(Bitmap bitmap, Rect src, Rect dst, Paint paint)  
贴图，参数一就是我们常规的Bitmap对象，参数二是源区域(这里是bitmap)，参数三是目标区域(应该在 canvas的位置和大小)，参数四是Paint画刷对象，因为用到了缩放和拉伸的可能，当原始Rect不等于目标Rect时性能将会有大幅损失。

* void  drawLine(float startX, float startY, float stopX, float stopY, Paint paint) 
画线，参数一起始点的x轴位置，参数二起始点的y轴位置，参数三终点的x轴水平位置，参数四y轴垂直位置，最后一个参数为Paint画刷对象。

* void  drawPoint(float x, float y, Paint paint) 
画点，参数一水平x轴，参数二垂直y轴，第三个参数为Paint对象。

* void drawText(String text, float x, float y, Paint paint)  
渲染文本，Canvas类除了上面的还可以描绘文字，参数一是String类型的文本，参数二x轴，参数三y轴，参数四是Paint对象。

* void  drawTextOnPath(String text, Path path, float hOffset, float vOffset, Paint paint) 
在路径上绘制文本，相对于上面第二个参数是Path路径对象

* void drawOval(@NonNull RectF oval, @NonNull Paint paint) 
绘制椭圆

* drawCircle(float cx, float cy, float radius, @NonNull Paint paint) 
绘制圆

* void drawArc(@NonNull RectF oval, float startAngle, float sweepAngle, boolean useCenter,
            @NonNull Paint paint)
绘制弧形、扇形

# 实例练习

## PorterDuffXfermode
PorterDuffXfermode设置的是两个图层交集区域的显示方式（如下图），dst是先画的图形，而src是后画的图形。 
当然，这些模式也不是经常使用的，用的最多的是，使用一张图片作为另一张图片的遮罩层，通过控制遮罩层的图形，来控制下面被遮罩图形的显示效果。其中最常用的就是通过DST_IN、SRC_IN模式来实现。
![](http://7q5c2h.com1.z0.glb.clouddn.com/PaintCanvas1.png)

### 圆角
效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/PaintCanvas2.png)

完整代码
```java
public class PaintCanvas extends View {
    public PaintCanvas(Context context) {
        super(context);
        initRoundRectImage();
    }

    public PaintCanvas(Context context, AttributeSet attrs) {
        super(context, attrs);
        initRoundRectImage();
    }

    public PaintCanvas(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initRoundRectImage();
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    public PaintCanvas(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
        initRoundRectImage();
    }

    Bitmap mBitmap, mNewBitmap;

    private void initRoundRectImage() {
        mBitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.dog);

        mNewBitmap = Bitmap.createBitmap(mBitmap.getWidth(), mBitmap.getHeight(), Bitmap.Config.ARGB_8888);

        Canvas canvas = new Canvas(mNewBitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        RectF rectF = new RectF(0, 0, mBitmap.getWidth(), mBitmap.getHeight());
        canvas.drawRoundRect(rectF, 80, 80, paint);
        paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_IN));
        canvas.drawBitmap(mBitmap, 0, 0, paint);

    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawBitmap(mNewBitmap, 0, 0, null);
    }
}
```
### 刮刮卡
效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/PaintCanvas3.gif)

完整代码
```java
public class PaintCanvas extends View {
    public PaintCanvas(Context context) {
        super(context);
        init();
    }

    public PaintCanvas(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public PaintCanvas(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init();
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    public PaintCanvas(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
        init();
    }

    private Bitmap mBgBitmap, mNewBitmap;
    private Path mPath;
    private Paint mPaint;
    private Canvas mCanvas;

    private void init() {
        mPaint = new Paint();
        mPaint.setAlpha(0);
        mPaint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_IN));//设置混合模式为DST_IN
        mPaint.setStyle(Paint.Style.STROKE);
        mPaint.setStrokeWidth(50);
        mPaint.setStrokeJoin(Paint.Join.ROUND);//Paint.Join.ROUND和Paint.Cap.ROUND设置Paint笔触和连接处更加圆滑一点
        mPaint.setStrokeCap(Paint.Cap.ROUND);

        mBgBitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.dog);//设置背景图
        mNewBitmap = Bitmap.createBitmap(mBgBitmap.getWidth(), mBgBitmap.getHeight(), Bitmap.Config.ARGB_8888); //设置遮罩图

        mCanvas = new Canvas(mNewBitmap);
        mCanvas.drawColor(Color.GRAY);

        mPath = new Path();

    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                mPath.reset();
                mPath.moveTo(event.getX(), event.getY());
                break;
            case MotionEvent.ACTION_MOVE:
                mPath.lineTo(event.getX(), event.getY());
                break;
        }
        mCanvas.drawPath(mPath, mPaint);
        invalidate();
        return true;

    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawBitmap(mBgBitmap, 0, 0, null); //画背景
        canvas.drawBitmap(mNewBitmap, 0, 0, null);//画灰色
    }
}
```

## Shader
Shader又被称为着色器、渲染器，主要用来实现一些列的渐变、渲染效果。Android中的Shader包括以下几种： 
BitmapShader——位图 
LinearGradient——线性 
RadialGrdient——光束 
SweepGradient——梯度 
ComposeShader——混合

除了第一个shader意外，其他的Shader都比较正常。而与其他的Shader所产生的渐变不同，BitmapShader产生的是一个图像，这个有点像Photoshop中的图像填充渐变。他的作用就是通过Paint对画布进行指定的Bitmap的填充，填充有以下几个模式可以选择。 
CLAMP 重复最后一个颜色至最后 
MIRROR 重复着色的图像水平或垂直方向已镜像方式填充会有翻转效果 
REPEAT 重复着色的图像水平或垂直方向

### 圆形头像
效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/PaintCanvas4.png)

完整代码
```java
 @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.dog);
        BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
        Paint paint = new Paint();
        paint.setShader(bitmapShader);
        canvas.drawCircle(300, 250, 200, paint);
    }
```

### 倒影
效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/PaintCanvas5.png)
完整代码
```java
public class PaintCanvas extends View {
    private Bitmap mSrcBitmap, mRefBitmap;
    private Paint mPaint;
    private PorterDuffXfermode mXfermode;

    public PaintCanvas(Context context) {
        super(context);
        initXfermodeShader();
    }

    public PaintCanvas(Context context, AttributeSet attrs) {
        super(context, attrs);
        initXfermodeShader();
    }

    public PaintCanvas(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initXfermodeShader();
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    public PaintCanvas(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
        initXfermodeShader();
    }

     private void initXfermodeShader() {
        mSrcBitmap = BitmapFactory.decodeResource(getResources(),
                R.mipmap.dog);

        Matrix matrix = new Matrix();
        matrix.setScale(1F, -1F);//实现图片垂直翻转

        mRefBitmap = Bitmap.createBitmap(mSrcBitmap, 0, 0,
                mSrcBitmap.getWidth(), mSrcBitmap.getHeight(), matrix, true);

        mPaint = new Paint();
        mPaint.setShader(new LinearGradient(0, mSrcBitmap.getHeight(), 0,
                mSrcBitmap.getHeight() + mSrcBitmap.getHeight() / 2,
                0x70ffffff, 0x00ffffff, Shader.TileMode.CLAMP));

        mXfermode = new PorterDuffXfermode(PorterDuff.Mode.DST_IN);
    }

    @Override
    protected void onDraw(Canvas canvas) {
//        canvas.drawColor(Color.BLACK);
        canvas.drawBitmap(mSrcBitmap, 0, 0, null);
        canvas.drawBitmap(mRefBitmap, 0, mSrcBitmap.getHeight(), null);
        mPaint.setXfermode(mXfermode);
        // 绘制渐变效果矩形
        canvas.drawRect(0, mSrcBitmap.getHeight(),
                mRefBitmap.getWidth(), mSrcBitmap.getHeight() * 2, mPaint);
        mPaint.setXfermode(null);
    }
}
```
# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
