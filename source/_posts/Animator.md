title: Android之属性动画Animator
date: 2015-11-23 11:21:35
tags: Animator
category: Android
---
> Android 3.0之前已有动画框架Animation（详见：[Android之视图动画Animation](http://wuxiaolong.me/2015/09/08/ViewAnimation/)），但存在一些局限性，当某个元素发生视图动画后，其响应事件位置还在动画前的地方。于是3.0之后，Google提出了属性动画。

# ObjectAnimator
```java
ObjectAnimator objectAnimator1 = ObjectAnimator.ofFloat(imageView, "translationX", 300);
objectAnimator1.setInterpolator(new AccelerateInterpolator());
objectAnimator1.setDuration(2000);
objectAnimator.setRepeatCount(ValueAnimator.INFINITE);//Animation.INFINITE 表示重复多次
objectAnimator.setRepeatMode(ValueAnimator.RESTART);//RESTART表示从头开始，REVERSE表示从末尾倒播
objectAnimator1.start();
```
第一个参数：操纵的view
第二个参数：操纵的动画属性值
第三个参数：可变数组参数

<!--more-->

## 动画属性值
translationX和translationY：增量控制view从它布局容器左上角坐标偏移
```java
ObjectAnimator.ofFloat(imageView, "translationX", 300f);
```
rotation、rotationX、rotationY：控制view绕支点进行2D或3D旋转
```java
ObjectAnimator.ofFloat(imageView, "rotation", 360);
```
scaleX、scaleY：控制view绕支点进行2D缩放
```java
ObjectAnimator.ofFloat(imageView, "scaleX", 1f, 0.5f，1f);
```
alpha：控制view透明度，默认是1（不透明），0完全透明（不可见）
```java
ObjectAnimator.ofFloat(imageView, "alpha", 1f, 0.5f);
```
x和y：描述view在容器最终位置

## 可变数组参数
可以有一个到N个，如果是一个值的话默认这个值是动画过渡值的结束值。如果有N个值，动画就在这N个值之间过渡。

## 动画监听
```java
 ObjectAnimator objectAnimator1 = ObjectAnimator.ofFloat(imageView, "alpha", 0.5f, 1f);
                objectAnimator1.addListener(new Animator.AnimatorListener() {
                    @Override
                    public void onAnimationStart(Animator animation) {
                        
                    }

                    @Override
                    public void onAnimationEnd(Animator animation) {

                    }

                    @Override
                    public void onAnimationCancel(Animator animation) {

                    }

                    @Override
                    public void onAnimationRepeat(Animator animation) {

                    }
                });
```
一般我们只关心onAnimationEnd，所以Android提供了AnimatorListenerAdapter：
```java
 ObjectAnimator objectAnimator1 = ObjectAnimator.ofFloat(imageView, "alpha", 0.5f, 1f);
                objectAnimator1.addListener(new AnimatorListenerAdapter() {
                    @Override
                    public void onAnimationEnd(Animator animation) {
                        super.onAnimationEnd(animation);
                    }
                });
```
# ValueAnimator 
ValueAnimator 本身不提供任何动画效果，像个数值 发生器，用来产生具有一点规律数字。
```java
ValueAnimator valueAnimator = ValueAnimator.ofInt(0, 100);
                valueAnimator.setTarget(imageView);
                valueAnimator.setDuration(2000).start();
                valueAnimator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
                    @Override
                    public void onAnimationUpdate(ValueAnimator animation) {
                        Int value = (Integer) animation.getAnimatedValue();
                        //TODO use the value
                        Toast.makeText(getApplicationContext(), "value=" + value, Toast.LENGTH_LONG).show();
                    }
                });
```

# PropertyValuesHolder
针对同一个对象多个属性，同时作用多种动画
```java
PropertyValuesHolder propertyValuesHolder1 = PropertyValuesHolder.ofFloat("translationX", 300f);
                PropertyValuesHolder propertyValuesHolder2 = PropertyValuesHolder.ofFloat("alpha", 1f, 0.5f);
                PropertyValuesHolder propertyValuesHolder3 = PropertyValuesHolder.ofFloat("scaleX", 1f, 0, 1f);
                PropertyValuesHolder propertyValuesHolder4 = PropertyValuesHolder.ofFloat("scaleY", 1f, 0, 1f);
                ObjectAnimator.ofPropertyValuesHolder(imageView, propertyValuesHolder1, propertyValuesHolder2, propertyValuesHolder3, propertyValuesHolder4)
                        .setDuration(5000).start();
```

# AnimatorSet 
与PropertyValuesHolder类似，但AnimatorSet多了playTogether（同时执行）、playSequentially（顺序执行）、play(objectAnimator1).with(objectAnimator2)、before、after这些方法协同工作。
```java
ObjectAnimator objectAnimator1 = ObjectAnimator.ofFloat(imageView, "alpha", 1f, 0.5f);
                ObjectAnimator objectAnimator2 = ObjectAnimator.ofFloat(imageView, "translationY", 300);
                ObjectAnimator objectAnimator3 = ObjectAnimator.ofFloat(imageView, "scaleX", 1f, 0, 1f);
AnimatorSet animatorSet = new AnimatorSet();
animatorSet.setDuration(5000);
animatorSet.playTogether(objectAnimator1, objectAnimator2,objectAnimator3);
animatorSet.start();
```

# xml使用属性动画
res下建立animator文件夹，然后建立res/animator/set_animator.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="2000"
    android:propertyName="alpha"
    android:valueFrom="0.1"
    android:valueTo="1.0"
    android:valueType="floatType" />
```
调用:
```java
 Animator animator = AnimatorInflater.loadAnimator(getApplicationContext(), R.animator.set_animator);
animator.setTarget(imageView);
animator.start();
```
动画组合
set标签，有一个orderring属性设置为together，还有另一个值：sequentially（表示一个接一个执行）。
```js
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:ordering="together">
    <objectAnimator
        android:duration="1000"
        android:propertyName="scaleX"
        android:valueFrom="1"
        android:valueTo="0.5" />
    <objectAnimator
        android:duration="1000"
        android:propertyName="scaleY"
        android:valueFrom="1"
        android:valueTo="0.5" />
</set>
```
# View的animate方法
Android 3.0后，谷歌给View增加animate方法直接驱动属性动画。
```java
 imageView.animate()
                .alpha(0.5f)
                .y(300)
                .setDuration(2000)
                //api min is 16
                .withStartAction(new Runnable() {
                    @Override
                    public void run() {

                    }
                })
                //api min is 16
                .withEndAction(new Runnable() {
                    @Override
                    public void run() {

                    }
                })
                .start();
```
# 布局动画
设置子View过渡动画
```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/parentLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="@dimen/activity_vertical_margin">
    <ImageView
        android:id="@+id/imageMove"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@mipmap/ic_launcher" />
</LinearLayout>

```
```java
 LinearLayout parentLayout = (LinearLayout) findViewById(R.id.parentLayout);
        ScaleAnimation scaleAnimation=new ScaleAnimation(0,1,0,1);
        scaleAnimation.setDuration(2000);
        LayoutAnimationController layoutAnimationController=new LayoutAnimationController(scaleAnimation,0.5f);
        layoutAnimationController.setOrder(LayoutAnimationController.ORDER_NORMAL);
        parentLayout.setLayoutAnimation(layoutAnimationController);
```

# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
