title: Android之画笔Paint和画布Canvas
date: 2015-11-30 10:45:38
tags: [Paint,Canvas]
category: Android
---
## Paint

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

## Canvas

* void drawRect(RectF rect, Paint paint) 
绘制区域，参数一为RectF一个区域

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