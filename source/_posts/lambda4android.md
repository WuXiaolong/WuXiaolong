title: Lambda for Android
date: 2016-03-28 20:59:20
tags: [lambda,retrolambda]
category: Android
---
> Lambda，是我去年学习RxJava时知道的，它让代码看起来更加简洁，但个人认为代码的可读性差了很多，因此一直没有去深入学习。

# 什么是lambda
lambda是一种匿名表达式，retrolambda使得Android能使用lambda特性，举个例子：
```
view.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                 Log.d("wxl", "retrolambda test");
            }
});
```
使用 lambda
```
view.setOnClickListener(
v -> Log.d("wxl", "retrolambda test"));
```
<!--more-->

# Android如何使用
这里是使用[Retrolambda](https://github.com/evant/gradle-retrolambda)的gradle插件，这样就可以在代码中使用lambda了。

## Android Studio配置Retrolambda
1. lambda需要安装JDK8，下载最新版本[jdk-8u73](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
2.  build.gradle
```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.5.0'
        classpath 'me.tatarka:gradle-retrolambda:3.2.4'//加上这句依赖，这会使用retrolambda来编译Java代码
    }
}
```
3.  app/build.gradle 
```
apply plugin: 'com.android.application'
apply plugin: 'me.tatarka.retrolambda'//加上这句加,入plugin声明
android {
	……
    compileOptions {//使用JAVA8语法解析
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
retrolambda {//指定将源码编译的级别，使用下列代码，会将代码编译到兼容1.6的字节码格式
    javaVersion JavaVersion.VERSION_1_6
}
```


# Android Studio自动生成lambda
![](http://7q5c2h.com1.z0.glb.clouddn.com/lambda4android1.png)
当配置Retrolambda成功后，Android Studio会有提示，按Alt+Enter键
![](http://7q5c2h.com1.z0.glb.clouddn.com/lambda4android2.png)
点击替换，这样就能自动生成，使用lambda了。到这里我就可以洗洗睡了，也很晚了，但为什么可以这样写呢，还是来简单了解lambda语法吧。

# lambda语法简介 
基本格式
```
input -> body
```
## intput种类
* 无输入 void
() -> body
```
new Thread(new Runnable() {
            @Override
            public void run() {
                Log.d("wxl", "retrolambda test");
            }
        });
```
使用 lambda
```
 new Thread(() -> Log.d("wxl", "retrolambda test"));
```

* 一个参数输入
x -> body
```
view.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                 Log.d("wxl", "retrolambda test");
            }
});
```
使用 lambda
```
view.setOnClickListener(
v -> Log.d("wxl", "retrolambda test"));
```
* 多个参数输入
(x, y) -> x + y;
```
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");
Collections.sort(names, new Comparator<String>() {
            @Override
            public int compare(String a, String b) {
                return b.compareTo(a);
}
        });
for (String name : names) {
     Log.d("wxl", name);
}
```
使用 lambda
```
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");
Collections.sort(names, (a, b) -> b.compareTo(a));
for (String name : names) {
     Log.d("wxl", name);
}
```

* 不省略型別
(int x, int y) -> x + y;

##  body 种类
* 什麼都不做
() -> {}
* 单行不需要有返回值，单行可省略{}
(x, y) -> x + y;
* 单行需要有返回值
(x, y) -> x + y//注意没有分号结尾
```
Observable.just("Hello", "RxJava")
          .map(new Func1<String, String>() {
                    @Override
                    public String call(String s) {
                        return s.toUpperCase();
                    }
          });
```
使用 lambda
```
 Observable.just("Hello", "RxJava")
           .map(s -> s.toUpperCase());
```
* 多行不需要有返回值
(x, y) ->{
  x * x;
  y * y;
}
* 多行需要有返回值
(x, y) ->{
  x * x;
  y * y;
  return x + y;
}
 
# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 附录
[Lambda表达式的意义](http://www.jianshu.com/p/f02b04344365)