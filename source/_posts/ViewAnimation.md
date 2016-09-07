title: Android之视图动画Animation
date: 2015-09-08 14:14:05
tags: Animation
category: CustomView
---
# 动画模式
View Animation主要有两种动画模式：
一种是tweened animation(渐变动画):
XML中：alpha
JavaCode：AlphaAnimation

XML中：scale
JavaCode：ScaleAnimation

一种是frame by frame(画面转换动画)
XML中：translate
JavaCode：TranslateAnimation

XML中：rotate
JavaCode：RotateAnimation

<!--more-->

#  alpha透明度
文件名:my_alpha_action.xml
```java
<?xml version="1.0" encoding="utf-8"?>
<alpha xmlns:android="http://schemas.android.com/apk/res/android"
android:fromAlpha="0.1"
android:toAlpha="1.0"
android:duration="3000"/>
```
透明度控制动画效果 alpha
浮点型值：
fromAlpha 属性为动画起始时透明度
toAlpha   属性为动画结束时透明度
说明:
0.0表示完全透明
1.0表示完全不透明
以上值取0.0-1.0之间的float数据类型的数字
长整型值：
duration  属性为动画持续时间
说明:
时间以毫秒为单位

在程序中引用XML资源核心代码如下：
```java
Animation mAnimation ;
mAnimation = AnimationUtils.loadAnimation(this, R.anim.anim);
TextView text = (TextView)findViewById(R.id.textview00);
text.setAnimation(mAnimation);
```
AlphaAnimation类对象构造
AlphaAnimation(float fromAlpha, float toAlpha)
第一个参数fromAlpha为 动画开始时候透明度
第二个参数toAlpha为 动画结束时候透明度
```java
private AlphaAnimation myAnimation_Alpha;
// 0.0表示完全透明，1.0表示完全不透明
myAnimation_Alpha=new AlphaAnimation(0.1f, 1.0f);
//设置动画持续时间，单位为毫秒
myAnimation_Alpha.setDuration(5000);
```

如何使用Java代码中的动画效果
```java
public void startAnimation (Animation animation)
```

# rotate旋转
文件名: my_rotate_action.xml
```java
<?xml version="1.0" encoding="utf-8"?>
<rotate  xmlns:android="http://schemas.android.com/apk/res/android"
android:interpolator="@android:anim/accelerate_decelerate_interpolator"
android:fromDegrees="0"
android:toDegrees="+350"
android:pivotX="50%"
android:pivotY="50%"
android:duration="3000" />
```
rotate 旋转动画效果
属性：interpolator 指定一个动画的插入器
在我试验过程中，使用android.res.anim中的资源时候发现有三种动画插入器
accelerate_decelerate_interpolator  加速-减速 动画插入器
accelerate_interpolator        加速-动画插入器
decelerate_interpolator        减速- 动画插入器

其他的属于特定的动画效果
浮点数型值:
fromDegrees 属性为动画起始时物件的角度
toDegrees   属性为动画结束时物件旋转的角度 可以大于360度
说明:
当角度为负数——表示逆时针旋转
当角度为正数——表示顺时针旋转
(负数from——to正数:顺时针旋转)
(负数from——to负数:逆时针旋转)
(正数from——to正数:顺时针旋转)
(正数from——to负数:逆时针旋转)
pivotX     属性为动画相对于物件的X坐标的开始位置
pivotY     属性为动画相对于物件的Y坐标的开始位置
说明:
以上两个属性值 从0%-100%中取值
50%为物件的X或Y方向坐标上的中点位置

长整型值，时间以毫秒为单位
duration  属性为动画持续时间

RotateAnimation类对象构造
```java
RotateAnimation(float fromDegrees, float toDegrees,
int pivotXType, float pivotXValue, int pivotYType, float pivotYValue)
```
第一个参数fromDegrees为动画起始时的旋转角度
第二个参数toDegrees为动画旋转到的角度
第三个参数pivotXType为动画在X轴相对于物件位置类型
第四个参数pivotXValue为动画相对于物件的X坐标的开始位置
第五个参数pivotYType为动画在Y轴相对于物件位置类型
第六个参数pivotYValue为动画相对于物件的Y坐标的开始位置

```java
//RotateAnimation类对象定义
private AlphaAnimation myAnimation_Alpha;
myAnimation_Rotate=new RotateAnimation(0.0f, +350.0f,
Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF, 0.5f);
//设置动画持续时间,3000毫秒
myAnimation_Rotate.setDuration(3000);
```
如何使用Java代码中的动画效果
```java
public void startAnimation (Animation animation)
```

# scale尺寸伸缩
文件名: my_scale_action.xml
```java
<?xml version="1.0" encoding="utf-8"?>
<scale xmlns:android="http://schemas.android.com/apk/res/android">
android:interpolator="@android:anim/accelerate_decelerate_interpolator"
android:fromXScale="0.0"
android:toXScale="1.4"

android:fromYScale="0.0"
android:toYScale="1.4"

android:pivotX="50%"
android:pivotY="50%"

android:fillAfter="false"
android:duration="700" />
```
尺寸伸缩动画效果 scale
属性：interpolator 指定一个动画的插入器
在我试验过程中，使用android.res.anim中的资源时候发现有三种动画插入器

accelerate_decelerate_interpolator  加速-减速 动画插入器
accelerate_interpolator        加速-动画插入器
decelerate_interpolator        减速- 动画插入器

其他的属于特定的动画效果
浮点型值：
fromXScale 属性为动画起始时 X坐标上的伸缩尺寸
toXScale   属性为动画结束时 X坐标上的伸缩尺寸

fromYScale 属性为动画起始时Y坐标上的伸缩尺寸
toYScale   属性为动画结束时Y坐标上的伸缩尺寸

说明:
以上四种属性值

0.0表示收缩到没有
1.0表示正常无伸缩
值小于1.0表示收缩
值大于1.0表示放大
pivotX     属性为动画相对于物件的X坐标的开始位置
pivotY     属性为动画相对于物件的Y坐标的开始位置

说明:
以上两个属性值 从0%-100%中取值
50%为物件的X或Y方向坐标上的中点位置

长整型值：
duration  属性为动画持续时间
说明:
时间以毫秒为单位

布尔型值:
fillAfter 属性 当设置为true ，该动画转化在动画结束后被应用

ScaleAnimation类对象构造
```java
ScaleAnimation(float fromX, float toX, float fromY, float toY,
int pivotXType, float pivotXValue, int pivotYType, float pivotYValue)
```
第一个参数fromX为动画起始时 X坐标上的伸缩尺寸
第二个参数toX为动画结束时 X坐标上的伸缩尺寸
第三个参数fromY为动画起始时Y坐标上的伸缩尺寸
第四个参数toY为动画结束时Y坐标上的伸缩尺寸
说明:
以上四种属性值
0.0表示收缩到没有
1.0表示正常无伸缩
值小于1.0表示收缩
值大于1.0表示放大
第五个参数pivotXType为动画在X轴相对于物件位置类型
第六个参数pivotXValue为动画相对于物件的X坐标的开始位置
第七个参数pivotXType为动画在Y轴相对于物件位置类型
第八个参数pivotYValue为动画相对于物件的Y坐标的开始位置

```java
private AlphaAnimation myAnimation_Alpha;
myAnimation_Scale =new ScaleAnimation(0.0f, 1.4f, 0.0f, 1.4f,
Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f);
//设置动画持续时间,700毫秒
myAnimation_Scale.setDuration(700);
```

如何使用Java代码中的动画效果
```java
public void startAnimation (Animation animation)
```

# translate位置转移
文件名: my_translate_action.xml
```java
<?xml version="1.0" encoding="utf-8"?>
<translate xmlns:android="http://schemas.android.com/apk/res/android">
<translate
android:fromXDelta="30"
android:toXDelta="-80"
android:fromYDelta="30"
android:toYDelta="300"
android:duration="800"
/>
```
translate 位置转移动画效果
整型值:
fromXDelta 属性为动画起始时 X坐标上的位置
toXDelta   属性为动画结束时 X坐标上的位置

fromYDelta 属性为动画起始时 Y坐标上的位置
toYDelta   属性为动画结束时 Y坐标上的位置
注意:
没有指定fromXType toXType fromYType toYType 时候，默认是以自己为相对参照物
长整型值：
duration  属性为动画持续时间
说明:
时间以毫秒为单位

TranslateAnimation类对象构造
```java
TranslateAnimation(float fromXDelta, float toXDelta,
float fromYDelta, float toYDelta)
```
第一个参数fromXDelta为动画起始时 X坐标上的移动位置
第二个参数toXDelta为动画结束时 X坐标上的移动位置
第三个参数fromYDelta为动画起始时Y坐标上的移动位置
第四个参数toYDelta为动画结束时Y坐标上的移动位置

```java
private TranslateAnimation myAnimation_Translate;
myAnimation_Translate =new TranslateAnimation(0f, 200f,400f, 400f);
//设置动画持续时间, 2000毫秒
myAnimation_Translate.setDuration(2000);
```
如何使用Java代码中的动画效果
```java
public void startAnimation (Animation animation)
```
# 参考
官网：[http://developer.android.com/intl/zh-cn/guide/topics/graphics/view-animation.html](http://developer.android.com/intl/zh-cn/guide/topics/graphics/view-animation.html)