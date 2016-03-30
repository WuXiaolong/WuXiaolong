title: RxBus
date: 2016-03-29 09:10:20
tags: [RxJava,Android,RxBus]
category: RxJava
---

> 相信大多数开发者都使用过EventBus或者Otto(我还未实践过)，作为事件总线通信库，如果你的项目已经加入RxJava和EventBus(或Otto)，那不妨用RxBus代替EventBus或者Otto，省去更多库的依赖。

# RxJava
之前已经分享过[RxJava](http://wuxiaolong.me/2016/01/18/rxjava/)知识，可以点击[RxJava](http://wuxiaolong.me/2016/01/18/rxjava/)，这里不再细说。

# RxBus 
假设你已经对[RxJava](http://wuxiaolong.me/2016/01/18/rxjava/)有所了解，再继续往下看。
<!--more-->
```
public class RxBus {
    private static RxBus mRxBus = null;
    /**
     * PublishSubject只会把在订阅发生的时间点之后来自原始Observable的数据发射给观察者
     */

    private Subject<Object, Object> mRxBusObserverable = new SerializedSubject<>(PublishSubject.create());

    public static synchronized RxBus getInstance() {
        if (mRxBus == null) {
            mRxBus = new RxBus();
        }
        return mRxBus;
    }

    public void post(Object o) {
        mRxBusObserverable.onNext(o);
    }

    public Observable<Object> toObserverable() {
        return mRxBusObserverable;
    }

    /**
     * 判断是否有订阅者
     */
    public boolean hasObservers() {
        return mRxBusObserverable.hasObservers();
    }
}
```
# 代码调用
先模拟一个订阅者，用hasObservers判断是否有订阅者，然后再发送。
```
public class RxBusActivity extends AppCompatActivity {
    private CompositeSubscription mCompositeSubscription;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_rx_bus);
        rxBusObservers();
        rxBusPost();
    }

    private void rxBusPost() {
        Log.d("wxl", "hasObservers=" + RxBus.getInstance().hasObservers());
        if (RxBus.getInstance().hasObservers()) {
            RxBus.getInstance().post(new TapEvent());
        }
    }

    private void rxBusObservers() {
        Subscription subscription = RxBus.getInstance()
                .toObserverable()
                .subscribe(new Action1<Object>() {
                    @Override
                    public void call(Object event) {
                        if (event instanceof TapEvent) {
                            //do something
                            Log.d("wxl", "rxBusHandle");
                        }
                    }
                });
        addSubscription(subscription);
    }


    public void addSubscription(Subscription subscription) {
        if (this.mCompositeSubscription == null) {
            this.mCompositeSubscription = new CompositeSubscription();
        }

        this.mCompositeSubscription.add(subscription);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (this.mCompositeSubscription != null) {
            this.mCompositeSubscription.unsubscribe();//取消注册，以避免内存泄露
        }
    }

    public class TapEvent {
    }
}
```

# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_AndroidProgrammer.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 完整代码
[https://github.com/WuXiaolong/AndroidSamples](https://github.com/WuXiaolong/AndroidSamples)

# EventBus源码解析
附上大牛们分析的EventBus源码，有兴趣可以看看
[EventBus源码研读](http://kymjs.com/code/2015/12/12/01/) by kymjs张涛
[EventBus 源码解析](http://a.codekk.com/detail/Android/Trinea/EventBus%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90) by Trinea


# 附录
[RxJava-Android-Samples](https://github.com/kaushikgopal/RxJava-Android-Samples/blob/master/app/src/main/java/com/morihacky/android/rxjava/rxbus/RxBus.java)




