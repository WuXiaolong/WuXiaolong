title: Android之LayoutParams
date: 2015-05-28 
categories: [Android]
tags: [Android,LayoutParams]
---

 LayoutParams相当于一个Layout的信息包，它封装了Layout的位置、高、宽等信息。假设在屏幕上一块区域是由一个Layout占领的，如果将一个View添加到一个Layout中，最好告诉Layout用户期望的布局方式，也就是将一个认可的layoutParams传递进去。
       可以这样去形容LayoutParams，在象棋的棋盘上，每个棋子都占据一个位置，也就是每个棋子都有一个位置的信息，如这个棋子在4行4列，这里的“4行4列”就是棋子的LayoutParams。

但LayoutParams类也只是简单的描述了宽高，宽和高都可以设置成三种值：
       
1，一个确定的值；
       
2，FILL_PARENT，即填满（和父容器一样大小）；
       
3，WRAP_CONTENT，即包裹住组件就好。

<!-- more -->

在JAVA中动态构建的布局，常常这样写：

```java
setLayoutParams(new LayoutParams(LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT));
```

上面这一句话其实是子对父的，也就是说，父布局下的子控件要设置这句话。

因为布局很多，虽然都继承至ViewGroup但是各个布局还是有很大的不同。

很显然上面这句应该这样写才算准确：

```java
setLayoutParams(new TableRow.LayoutParams(TableRow.LayoutParams.FILL_PARENT,TableRow.LayoutParams.FILL_PARENT));
```

这表示这个子控件的父布局是一个TableRow , 这样的LayoutParams 太多，所以应明确指明。

 

下面分别说下两个常用到布局:

1. FrameLayout下动态设置子控件居中，动态用JAVA代码要这样实现:

```java
FrameLayout.LayoutParams lytp = new FrameLayout.LayoutParams(80,LayoutParams.WRAP_CONTENT);
lytp .gravity = Gravity.CENTER;
btn.setLayoutParams(lytp);
```

2. RelativeLayout下动态设置子控件居中：

```java
RelativeLayout.LayoutParams lp=new RelativeLayout.LayoutParams(LayoutParams.WRAP_CONTENT,LayoutParams.WRAP_CONTENT); 
lp.addRule(RelativeLayout.ALIGN_PARENT_RIGHT, RelativeLayout.TRUE); 
lp.addRule(RelativeLayout.ALIGN_PARENT_TOP, RelativeLayout.TRUE); 
btn1.setLayoutParams(lp);
```
