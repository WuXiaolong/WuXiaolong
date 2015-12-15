title: Android之美化控件Shape
categories: [Android]
tags: Shape
date: 2013-07-09
---

除了使用drawable这样的图片外，随着图片资源增多，程序也越来越大，今天我们自定义图形shape的方法。

```java
	<shape>
            <!-- 实心 -->
            <solid android:color="#ff9d77"/>
            <!-- 渐变 -->
            <gradient
                android:startColor="#ff8c00"
                android:endColor="#FFFFFF"
                android:angle="270" />
            <!-- 描边 -->
            <stroke
                android:width="2dp"
                android:color="#dcdcdc" />
            <!-- 圆角 -->
            <corners
                android:radius="2dp" />
            <padding
                android:left="10dp"
                android:top="10dp"
                android:right="10dp"
                android:bottom="10dp" />
        </shape>
```
<!--more-->
对于上面， shape的定义
* shape类型
android:shape=["rectangle" | "oval" | "line" | "ring"]  
shape的形状，默认为矩形，可以设置为矩形（rectangle）、椭圆形(oval)、线性形状(line)、环形(ring)  
下面的属性只有在android:shape="ring时可用：  
android:innerRadius  尺寸，内环的半径。  
android:innerRadiusRatio 浮点型，以环的宽度比率来表示内环的半径，  

* solid
实心，填充的意思
android:color指定填充的颜色

* gradient
渐变
android:startColor 开始的颜色
android:endColor 渐变结束的颜色
android:angle 渐变角度，必须为45的整数倍，当angle=0时，渐变色是从左向右。 然后逆时针方向转，当angle=90时为从下往上
android:type="linear"，即线性渐变，可以指定渐变为径向渐变，android:type="radial"，径向渐变需要指定半径android:gradientRadius="50"

* stroke
描边缘
android:width="2dp" 描边的宽度
android:color 描边的颜色
我们还可以把描边弄成虚线的形式，设置方式为：android:dashWidth="5dp" android:dashGap="3dp"
其中android:dashWidth表示'-'这样一个横线的宽度，android:dashGap表示之间隔开的距离。

* corners
拐角，radius属性为半径，android:radius为角的弧度，值越大角越圆。

* padding：间隔
这个就不用多说了，XML布局文件中经常用到。

```java
<?xml version="1.0" encoding="utf-8"?>
<selector
    xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" >
        <shape>
            <gradient
                android:startColor="#ff8c00"
                android:endColor="#FFFFFF"
                android:angle="270" />
            <stroke
                android:width="2dp"
                android:color="#dcdcdc" />
            <corners
                android:radius="10dp" />
            <padding
                android:left="10dp"
                android:top="10dp"
                android:right="10dp"
                android:bottom="10dp" />
        </shape>
    </item>

    <item android:state_focused="true" >
        <shape>
            <gradient
                android:startColor="#ffc2b7"
                android:endColor="#ffc2b7"
                android:angle="270" />
            <stroke
                android:width="2dp"
                android:color="#dcdcdc" />
            <corners
                android:radius="10dp" />
            <padding
                android:left="10dp"
                android:top="10dp"
                android:right="10dp"
                android:bottom="10dp" />
        </shape>
    </item>

    <item>       
        <shape>
            <gradient
                android:startColor="#ff9d77"
                android:endColor="#ff9d77"
                android:angle="270" />
            <stroke
                android:width="2dp"
                android:color="#fad3cf" />
            <corners
                android:radius="10dp" />
            <padding
                android:left="10dp"
                android:top="10dp"
                android:right="10dp"
                android:bottom="10dp" />
        </shape>
    </item>
</selector>
```
以上几个item的区别主要是体现在state_pressed按下或state_focused获得焦点时，当当来判断显示什么类型，而没有state_xxx属性的item可以看作是常规状态下。

常规状态效果图：
![常规状态效果图](http://wuxiaolong.qiniudn.com/2013-07-09-shape-1.png)
state_pressed按下或state_focused获得焦点：
![常规状态效果图](http://wuxiaolong.qiniudn.com/2013-07-09-shape-2.png)
