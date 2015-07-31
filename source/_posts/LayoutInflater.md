title: Android之LayoutInflate
date: 2015-04-23 
categories: [Android]
tags: [Android,LayoutInflate]
---
【转载】[Android获取LayoutInflater对象的方法总结](http://blog.csdn.net/bigconvience/article/details/26582497)

在写Android程序时，有时候会编写自定义的View，使用Inflater对象来将布局文件解析成一个View。本文主要目的是总结获取LayoutInflater对象的方法。

1、若能获取context对象，可以有以下几种方法：

```java 
LayoutInflater inflater = (LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);  
View child = inflater.inflate(R.layout.child, null);  
```
<!-- more -->

or

```java
LayoutInflater inflater = LayoutInflater.from(context);  
View child = inflater.inflate(R.layout.child, null);  
```

inflate实现源码如下：

```java
public static View inflate(Context context, int resource, ViewGroup root) {  
    LayoutInflater factory = LayoutInflater.from(context);  
    return factory.inflate(resource, root);  
}  
```

LayoutInflater.from(context)实际上是对方法1的包装，可参考以下源码：

```java
public static LayoutInflater from(Context context) {  
    LayoutInflater LayoutInflater =  
            (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);  
    if (LayoutInflater == null) {  
        throw new AssertionError("LayoutInflater not found.");  
    }  
    return LayoutInflater;  
}  
```

2、在一个Activity中，可以有以下方法：

```java
View child = getLayoutInflater().inflate(R.layout.child, item, false); 
```

or

```java
View view;   
LayoutInflater inflater = (LayoutInflater)   getContext().getSystemService(Context.LAYOUT_INFLATER_SERVICE);   
view = inflater.inflate(R.layout.mylayout, null);  
```
方法1和方法2其实都是对context().getSystemService()的使用。

3、使用View的静态方法：

```java
View view=View.inflate(context, R.layout.child, null)  
```

