title: Android之美化控件Shape
categories: [Android]
tags: [Android,Shape]
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

* solid：实心，就是填充的意思

	android:color指定填充的颜色

* gradient：渐变

	android:startColor和android:endColor分别为起始和结束颜色，ndroid:angle是渐变角度，必须为45的整数倍。

	另外渐变默认的模式为android:type="linear"，即线性渐变，可以指定渐变为径向渐变，android:type="radial"，径向渐变需要指定半径android:gradientRadius="50"。

* stroke：描边

	android:width="2dp" 描边的宽度，android:color 描边的颜色。
	我们还可以把描边弄成虚线的形式，设置方式为：
	android:dashWidth="5dp" 
	android:dashGap="3dp"
	其中android:dashWidth表示'-'这样一个横线的宽度，android:dashGap表示之间隔开的距离。

* corners：圆角
	android:radius为角的弧度，值越大角越圆。
	我们还可以把四个角设定成不同的角度，方法为：

```java
	<corners 
        android:topRightRadius="20dp"    右上角
        android:bottomLeftRadius="20dp"    右下角
        android:topLeftRadius="1dp"    左上角
        android:bottomRightRadius="0dp"    左下角
	/>
```
	这里有个地方需要注意，bottomLeftRadius是右下角，而不是左下角，这个有点郁闷，不过不影响使用，记得别搞错了就行。

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
