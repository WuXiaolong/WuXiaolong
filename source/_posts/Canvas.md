title: Android 画布Canvas
date: 2016-08-22 10:19:54
tags: Canvas
category: CustomView
--- 
# 绘制方法
以drawXXX为主的绘制方法，api如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas1.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas2.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas3.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas4.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

## drawARGB
```
void drawARGB (int a,int r,int g,int b)
```
统一颜色绘制，四个参数取值范围0~255

# drawArc
```
void drawArc (RectF oval, 
                float startAngle, 
                float sweepAngle, 
                boolean useCenter, 
                Paint paint)
```
绘制弧面或弧线。对于绘制对应图形的填充面，还是图形的轮廓线，这在于画笔Paint中的setStyle。
oval：矩形；
startAngle：起点角度，0度的角度对应于0度的几何角（在手表3点钟）；
sweepAngle：顺时针扫过的角度；
useCenter：弧面或弧线；
paint：画笔

* 弧面
```
RectF rectF = new RectF(100f, 100f, 500f, 500f);
canvas.drawArc(rectF, 0, 150, true, mPaint);
```
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas5.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
* 弧线
```
 RectF rectF = new RectF(100f, 100f, 500f, 500f);
 canvas.drawArc(rectF, 0, 150, false, mPaint);
```
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas6.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

## drawBitmap
绘制使用指定的矩阵的位图。
```
//方法1
void drawBitmap (Bitmap bitmap, 
                float left, 
                float top, 
                Paint paint) 

//方法2               
void drawBitmap (Bitmap bitmap, 
                Rect src, 
                Rect dst, 
                Paint paint)
//方法3               
void drawBitmap (Bitmap bitmap, 
                Matrix matrix, 
                Paint paint)              
```
例子代码
```
mBitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.logo);

canvas.drawBitmap(mBitmap, 0, 0, mPaint);

//绘制Bitmap的一部分，并对其拉伸
//srcRect绘制Bitmap的哪一部分
Rect src = new Rect(0, 0, mBitmap.getWidth(), mBitmap.getHeight() / 3);
//dstRecF绘制的Bitmap拉伸到哪里
RectF dst = new RectF(0, mBitmap.getHeight(), canvas.getWidth(), mBitmap.getHeight() + 200);
canvas.drawBitmap(mBitmap, src, dst, mPaint);

Matrix matrix = new Matrix();
matrix.postTranslate(0, mBitmap.getHeight() + 200);
canvas.drawBitmap(mBitmap, matrix, mPaint);
```
效果图从上至下，分别对应方法1-3
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas7.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

# drawBitmapMesh
```
void drawBitmapMesh (Bitmap bitmap, 
                int meshWidth, 
                int meshHeight, 
                float[] verts, 
                int vertOffset, 
                int[] colors, 
                int colorOffset, 
                Paint paint)
```
绘制网格顶点被均匀地分布在该位图
bitmap：位图
meshWidth：横向上把该源位图划成成多少格
meshHeight：竖向上把该源位图划成成多少格
verts：长度为(meshWidth + 1) * (meshHeight + 1) * 2的数组，它记录了扭曲后的位图各顶点位置
vertOffset：控制verts数组中从第几个数组元素开始才对bitmap进行扭曲
colors：可以为null，指定在每个顶点，其值由对应的位图颜色相乘内插的颜色。如果不为空，必须有至少（meshWidth+ 1）*（meshHeight+ 1）+ colorOffset数组中的值。
colorOffset	int: Number of color elements to skip before drawing
paint：画笔，可以为null

效果参考博客[Android Canvas的drawBitmapMesh实现扭曲图像](http://blog.csdn.net/u010947098/article/details/44731781)

## drawCircle
```
void drawCircle (float cx, 
                float cy, 
                float radius, 
                Paint paint)
```
绘制圆
cx，cy代表圆心的坐标
radius圆的半径
paint画笔
```
canvas.drawCircle(500f, 500f, 200, mPaint);
```
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas8.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

## drawColor
```
void drawColor (int color)
```
设置画布颜色

## drawLine
```
void drawLine (float startX, 
                float startY, 
                float stopX, 
                float stopY, 
                Paint paint)
void drawLines (float[] pts, 
                Paint paint)                
void drawLines (float[] pts, 
                int offset, 
                int count, 
                Paint paint)                
```
画线
方法1
startX，startY起点坐标
stopX，stopY终点坐标
paint画笔

方法2
pts：绘制直线的端点数组，每条直线占用4个数据，即起终点坐标。
paint：绘制直线所使用的画笔。

方法2
pts：绘制直线的端点数组，每条直线占用4个数据，即起终点坐标。
offset：跳过的数据个数，取值为4的倍数。
count：实际参与绘制的数据个数。
paint：绘制直线所使用的画笔。
```
canvas.drawLine(100f,100f,500f,500f,mPaint);

```
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas9.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

```
float[] pts = {100f, 100f, 400f, 400f,
                400f, 400f, 250f, 560f,
                250f, 560f, 400f, 800f,
                400f, 800f, 280f, 880f};
canvas.drawLines(pts, mPaint);

```
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas10.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
```
float[] pts = {100f, 100f, 400f, 400f,
                400f, 400f, 250f, 560f,
                250f, 560f, 400f, 800f,
                400f, 800f, 280f, 880f};
//有选择地绘制直线
canvas.drawLines(pts, 4, 8, mPaint);

```
跳过前4个数据，绘制后面8的数据，即绘制了第二第三个点：
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas11.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

# drawOval
```
void drawOval (RectF oval,Paint paint)
```
绘制椭圆
```
 RectF rectF = new RectF(100f, 100f, 600f, 500f);
 //等同于圆的效果
 //RectF rectF = new RectF(100f, 100f, 500f, 500f);
 canvas.drawOval(rectF, mPaint);
```
![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas12.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

## drawPath
```
void drawPath (Path path,Paint paint)
```
例子
```
Path path = new Path();
//向Path中加入Arc
RectF arcRecF = new RectF(0, 0, 500, 500);
path.addArc(arcRecF, 0, 135);
canvas.drawPath(path, mPaint);
```
更多Path，详见博客[android绘图之Path总结](http://ghui.me/post/2015/10/android-graphics-path/)
 
# drawPoint
```
void drawPoint (float x, 
                float y, 
                Paint paint)    
```
绘制点，x，y分别是点坐标
```
void drawPoints (float[] pts, 
                int offset, 
                int count, 
                Paint paint)
void drawPoints (float[] pts, 
                Paint paint)                
```
drawPoints与drawLines类似

# drawRGB
```
void drawRGB (int r, 
                int g, 
                int b)    
```
RGB绘制画布颜色，取值也是0~255
            
# drawRect
```
void drawRect (float left, 
                float top, 
                float right, 
                float bottom, 
                Paint paint)
```
绘制矩形，前四个参数分别表示矩形的左，顶，右，低
```
void drawRect (Rect r,Paint paint)
void drawRect (RectF rect,Paint paint)
```  
Rect 和 RectF区别是Rect初始化 是int，RectF 是left
```
 canvas.drawRect(100f,100f,500f,500f,mPaint);
```
 ![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas13.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10) 

# drawRoundRect
```
void drawRoundRect (RectF rect, 
                float rx, 
                float ry, 
                Paint paint)
```
绘制圆矩形
rx：圆角x方向的半径
ry：圆角y方向的半径
```
RectF rectF = new RectF(100f, 100f, 500f, 500f);
canvas.drawRoundRect(rectF, 50, 150, mPaint);
```
 ![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas14.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10) 

# drawText

```
void drawText (CharSequence text, 
                int start, 
                int end, 
                float x, 
                float y, 
                Paint paint)
void drawText (String text, 
                float x, 
                float y, 
                Paint paint)
void drawText (String text, 
                int start, 
                int end, 
                float x, 
                float y, 
                Paint paint)                                
```
绘制文本
start：指的文本从哪个开始；end：文本结束的位置；x，y：文本起点
```
 mPaint.setTextSize(50);
 String text = "我的微信公众号：吴小龙同学";
 canvas.drawText(text, 2, text.length(), 100, 100, mPaint)
 canvas.drawText("我的微信公众号：吴小龙同学", 100, 400, mPaint);
```
 ![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas15.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10) 

drawTextOnPath
```
void drawTextOnPath (String text, 
                Path path, 
                float hOffset, 
                float vOffset, 
                Paint paint)
void drawTextOnPath (char[] text, 
                int index, 
                int count, 
                Path path, 
                float hOffset, 
                float vOffset, 
                Paint paint)                
```
沿着Path绘制一段文字

```
 Path path = new Path();
 //Path.Direction.CW，沿外环；Path.Direction.CCW，沿内环
 path.addCircle(500, 500, 200, Path.Direction.CW);
 mPaint.setTextSize(50);
 // 绘制路径
 canvas.drawPath(path, mPaint);
 String text = "我的微信公众号：吴小龙同学";
 canvas.drawTextOnPath(text, path, 0f, 0f, mPaint);
```
 ![](http://7q5c2h.com1.z0.glb.clouddn.com/canvas16.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10) 
 
# 裁剪方法
以clipXXX为主的

# 变换方法
以scale、skew、translate和rotate

# 画布锁定和还原
以saveXXX和restoreXXX构成的画布锁定和还原