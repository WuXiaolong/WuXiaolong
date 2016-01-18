title: rxjava
date: 2016-01-18 15:50:14
tags: RxJava
category: RxJava
---
# 创建操作
## create
```java
 Observable.create(new Observable.OnSubscribe<String>() {
            @Override
            public void call(Subscriber<? super String> subscriber) {
                subscriber.onNext("Hello");
                subscriber.onNext("RxJava");
                subscriber.onCompleted();
            }
        })
        .subscribe(new Observer<String>() {
            @Override
            public void onCompleted() {
                Log.i("wxl", "onCompleted");
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(String s) {
                Log.i("wxl", "onNext=" + s);
            }
        });
```
<!--more-->
除了 Observer 接口之外，RxJava 还内置了一个实现了 Observer 的抽象类：Subscriber。 Subscriber 对 Observer 接口进行了一些扩展，但他们的基本使用方式是完全一样的：
```java
 .subscribe(new Subscriber() {
            @Override
            public void onCompleted() {
                Log.i("wxl", "onCompleted");
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(Object o) {
                Log.i("wxl", "onNext=" + o);
            }
        });
```
## from
```java
        String[] froms={"Hello","RxJava"};
        Observable.from(froms)
        .subscribe(new Observer<String>() {
            @Override
            public void onCompleted() {
                Log.i("wxl", "onCompleted");
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(String s) {
                Log.i("wxl", "onNext=" + s);
            }
        });
```

## just
just函数，它接受一至九个参数，返回一个按参数列表顺序发射这些数据的Observable。
```java
Observable.just("Hello","RxJava")
          .subscribe(new Observer<String>() {
            @Override
            public void onCompleted() {
                Log.i("wxl", "onCompleted");
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(String s) {
                Log.i("wxl", "onNext=" + s);
            }
        });
```
以上打印结果都是：
```
com.wuxiaolong.apksample I/wxl: onNext=Hello
com.wuxiaolong.apksample I/wxl: onNext=RxJava
com.wuxiaolong.apksample I/wxl: onCompleted
```

# 变换操作
## Map
操作符对原始Observable发射的每一项数据应用一个你选择的函数，然后返回一个发射这些结果的Observable。
如下，将原始Observable数据转化成大写，再发射：
```java
 Observable.just("Hello", "RxJava")
                .map(new Func1<String, String>() {
                    @Override
                    public String call(String s) {
                        return s.toUpperCase();
                    }
                })
                .subscribe(new Observer<String>() {
                    @Override
                    public void onCompleted() {
                        Log.i("wxl", "onCompleted");
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onNext(String s) {
                        Log.i("wxl", "onNext=" + s);
                    }
                });
```
打印结果：
```
com.wuxiaolong.apksample I/wxl: onNext=HELLO
com.wuxiaolong.apksample I/wxl: onNext=RXJAVA
com.wuxiaolong.apksample I/wxl: onCompleted
```

## flatMap
flatMap()接收一个Observable的输出作为输入，同时输出另外一个Observable。理解flatMap的关键点在于，flatMap输出的新的Observable正是我们在Subscriber想要接收的，比如这里输出单个的字符串。
```java
List<String> list = new ArrayList<>();
        list.add("Hello");
        list.add("RxJava");
Observable.from(list)
                .flatMap(new Func1<String, Observable<String>>() {
                    @Override
                    public Observable<String> call(String s) {
                        return Observable.just(s.toUpperCase());
                    }
                })
                .subscribe(new Observer<String>() {
                    @Override
                    public void onCompleted() {
                        Log.i("wxl", "onCompleted");
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onNext(String s) {
                        Log.i("wxl", "onNext=" + s);
                    }
                });
```
打印结果：
```
com.wuxiaolong.apksample I/wxl: onNext=HELLO
com.wuxiaolong.apksample I/wxl: onNext=RXJAVA
com.wuxiaolong.apksample I/wxl: onCompleted
```