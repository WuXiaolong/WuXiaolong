title: RxJava
date: 2016-01-18 15:50:14
tags: RxJava
category: RxJava
---
# RxJava是什么
a library for composing asynchronous and event-based programs using observable sequences for the Java VM（一个对于构成使用的Java虚拟机观察序列异步和基于事件的程序库）。
github：[https://github.com/ReactiveX/RxJava](https://github.com/ReactiveX/RxJava)
<!--more-->
## 观察者模式
RxJava的世界里，我们有四种角色：
Observable(被观察者)、Observer(观察者)
Subscriber(订阅)、Subject
Observable和Subject是两个“生产”实体，Observer和Subscriber是两个“消费”实体。Observable 和 Observer 通过 subscribe() 方法实现订阅关系，从而 Observable 可以在需要的时候发出事件来通知 Observer。

## 回调方法

Subscribe方法用于将观察者连接到Observable，你的观察者需要实现以下方法：
* onNext(T item)
Observable调用这个方法发射数据，方法的参数就是Observable发射的数据，这个方法可能会被调用多次，取决于你的实现。

* onError(Exception ex)
当Observable遇到错误或者无法返回期望的数据时会调用这个方法，这个调用会终止Observable，后续不会再调用onNext和onCompleted，onError方法的参数是抛出的异常。

* onComplete
正常终止，如果没有遇到错误，Observable在最后一次调用onNext之后调用此方法。


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
操作符对原始Observable发射的每一项数据应用一个你选择的函数，然后返回一个发射这些结果。
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
flatMap()接收一个Observable的输出作为输入，然后作为一个新的Observable再发射。理解flatMap的关键点在于，flatMap输出的新的Observable正是我们在Subscriber想要接收的，比如这里输出单个的字符串。
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
# Scan
一个累加器函数，操作符对原始Observable发射的第一项数据应用一个函数，然后将那个函数的结果作为自己的第一项数据发射。
```java
 Observable.just(1, 2, 3, 4, 5)
         .scan(new Func2<Integer, Integer, Integer>() {
             @Override
             public Integer call(Integer integer, Integer integer2) {
                 return integer + integer2;
             }
         })
         .subscribe(new Observer<Integer>() {
             @Override
             public void onCompleted() {
                 Log.i("wxl", "onCompleted");
             }
             @Override
             public void onError(Throwable e) {
             }
             @Override
             public void onNext(Integer integer) {
                 Log.i("wxl", "onNext=" + integer);
             }
         });
```
第一次发射得到1，作为结果与2相加；发射得到3，作为结果与3相加，以此类推，打印结果：
```
 I/wxl: onNext=1
 I/wxl: onNext=3
 I/wxl: onNext=6
 I/wxl: onNext=10
 I/wxl: onNext=15
 I/wxl: onCompleted
```

## GroupBy
按照指定的规则来分组元素。

# 过滤操作
## Filter
观测序列中只有通过的数据才会被发射。
```java
Observable.just(4, 2, 1, 7, 5)
        .filter(new Func1<Integer, Boolean>() {
            @Override
            public Boolean call(Integer integer) {
                return integer > 3;
            }
        })
        .subscribe(new Observer<Integer>() {
            @Override
            public void onCompleted() {
                Log.i("wxl", "onCompleted");
            }
            @Override
            public void onError(Throwable e) {
            }
            @Override
            public void onNext(Integer integer) {
                Log.i("wxl", "onNext=" + integer);
            }
        });
```
过滤小于3的，打印结果：
```
I/wxl: onNext=4
I/wxl: onNext=7
I/wxl: onNext=5
I/wxl: onCompleted
```
## take()、takeLast()
```java
 .take(3)
```
只发射前N个元素
```java
 .takeLast(3)
```
只发射最后N个元素

## First、last
只发射第一个元素或者最后一个元素

## Skip、SkipLast
```java 
.skip(2)
```
来创建一个不发射前两个元素而是发射它后面的那些数据的序列

## distinct
仅处理一次，可以处理去除重复的数据

## ElementAt
仅从一个序列中发射第n个元素然后就完成了
```java 
Observable.just(1, 2, 3, 4, 5, 6)
                .elementAt(3)
                .subscribe(……);
```
打印结果：4
## Sample 
定期发射Observable最近发射的数据项
```java
 .sample(1000, TimeUnit.MILLISECONDS)
```
# 合并操作
## Merge
合并多个Observables的发射物，多输入，单输出
```java
Observable<Integer> observable1 = Observable.just(1, 3, 5);
Observable<Integer> observable2 = Observable.just(2, 4, 6);
Observable.merge(observable1,observable2)
        .subscribe(new Observer<Integer>() {
            @Override
            public void onCompleted() {
                Log.i("wxl", "onCompleted");
            }
            @Override
            public void onError(Throwable e) {
            }
            @Override
            public void onNext(Integer integer) {
                Log.i("wxl", "onNext=" + integer);
            }
        });
```
打印结果：
```
 I/wxl: onNext=1
 I/wxl: onNext=3
 I/wxl: onNext=5
 I/wxl: onNext=2
 I/wxl: onNext=4
 I/wxl: onNext=6
 I/wxl: onCompleted
```
## zip
合并两个或者多个Observables发射出的数据项，根据指定的函数Func2变换它们，并发射一个新值。
```java
Observable<Integer> observable1 = Observable.just(1, 3, 5);
Observable<Integer> observable2 = Observable.just(2, 4, 6, 9);
Observable.zip(observable1, observable2, new Func2<Integer, Integer, Integer>
    @Override
    public Integer call(Integer integer, Integer integer2) {
        return integer + integer2;
    }
})
        .subscribe(new Observer<Integer>() {
            @Override
            public void onCompleted() {
                Log.i("wxl", "onCompleted");
            }
            @Override
            public void onError(Throwable e) {
            }
            @Override
            public void onNext(Integer integer) {
                Log.i("wxl", "onNext=" + integer);
            }
        });
```
打印结果：
```
I/wxl: onNext=3
I/wxl: onNext=7
I/wxl: onNext=11
I/wxl: onCompleted
```
## join

## startWith
```java
 .startWith(1)
```
在数据序列的开头插入一条指定的项1
# Schedulers
调度器，解决Android主线程问题，有5种：
* Schedulers.io()
这个调度器时用于I/O操作（读写文件、读写数据库、网络信息交互等）

* Schedulers.computation()
这个是计算工作默认的调度器，它与I/O操作无关。它也是许多RxJava方法的默认调度器：buffer()，debounce()，delay()，interval()，sample()，skip()

* Schedulers.immediate()
这个调度器允许你立即在当前线程执行你指定的工作。它是timeout()，timeInterval()，以及timestamp()方法默认的调度器。

* Schedulers.newThread()
它为指定任务启动一个新的线程。

* Schedulers.trampoline()
当我们想在当前线程执行一个任务时，并不是立即，我们可以用.trampoline()将它入队。这个调度器将会处理它的队列并且按序运行队列中每一个任务。它是repeat()和retry()方法默认的调度器。
## SubscribeOn、ObserveOn
subscribeOn()：事件产生的线程
observeOn()：事件消费的线程，AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行。
```java
.subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
.observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
```

未完……

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 附录
[RxJava Essentials CN](http://rxjava.yuxingxin.com/)
[ReactiveX文档中文翻译](https://mcxiaoke.gitbooks.io/rxdocs/content/)
[给 Android 开发者的 RxJava 详解](http://gank.io/post/560e15be2dca930e00da1083)

