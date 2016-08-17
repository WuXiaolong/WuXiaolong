title: Android MVP+Retrofit+RxJava实践小结
date: 2016-06-12 11:07:23
tags: [MVP,Retrofit,RxJava]
category: Android
---

关于MVP、Retrofit、RxJava，之前已经分别做了分享，如果您还没有阅读过，可以猛戳：
1、[Android MVP 实例](http://wuxiaolong.me/2015/09/23/AndroidMVPSample/)
2、[Android Retrofit 2.0使用](http://wuxiaolong.me/2016/01/15/retrofit/)
3、[RxJava](http://wuxiaolong.me/2016/01/18/rxjava/)
4、[RxBus](http://wuxiaolong.me/2016/04/07/rxbus/)

假设，您对MVP、Retrofit、RxJava已经有了一点了解，那么我们开始本文：
<!--more-->
# Android MVP优化

1、MVP绑定Activity（Fragment）生命周期
按照之前的文章，每个Presenter都得初始化和销毁，我新加MvpActivity(MvpFragment)，加了抽象方法`protected abstract P createPresenter();`这样做的目的在需要使用MVP的地方，可以继承MvpActivity(MvpFragment)，然后初始化和销毁就不用手动一个个去加了。

2、接口请求等还是放到MVP的P中
![](http://7q5c2h.com1.z0.glb.clouddn.com/androidmvpsample2.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
这个图片，在当时写MVP文章时给出的，实际开发中，我发现每个都这样写，实在是增加了不少代码，然接口请求放到P中，还有个好处，就是MVP绑定Activity（Fragment）生命周期，当onDestroy时取消RXJava注册，以避免内存泄露。

# 代码
## 目录结构
如图，有个大致了解：
![](http://7q5c2h.com1.z0.glb.clouddn.com/mvpRetrofitRxjava1.jpg)
mvp：所有的mvp都放在这个包下
retrofit：Retrofit接口和配置文件
rxjava：RxJava一些回调设置
ui：Activity或fragment，建议按功能再细分包

## 核心代码
还是就贴出核心代码吧，源码在我的github上（[https://github.com/WuXiaolong/AndroidMVPSample](https://github.com/WuXiaolong/AndroidMVPSample)）。

MainActivity入口，还是演示的之前的MVP的天气的接口，接口请求方法放在Presenter。

### MvpActivity
Presenter绑定Activity（Fragment）生命周期
```java
public abstract class MvpActivity<P extends BasePresenter> extends BaseActivity {
    protected P mvpPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        mvpPresenter = createPresenter();
        super.onCreate(savedInstanceState);
    }

    protected abstract P createPresenter();

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (mvpPresenter != null) {
            mvpPresenter.detachView();
        }
    }
}
```

### MainPresenter
apiStores.loadData方法是Retrofit做的网络请求，回调是RxJava完成的。
```java
public class MainPresenter extends BasePresenter<MainView> {

    public MainPresenter(MainView view) {
        attachView(view);
    }
   
    public void loadData(String cityId) {
        mvpView.showLoading();
        addSubscription(apiStores.loadData(cityId),
                new SubscriberCallBack<>(new ApiCallback<MainModel>() {
                    @Override
                    public void onSuccess(MainModel model) {
                        mvpView.getDataSuccess(model);
                    }

                    @Override
                    public void onFailure(int code, String msg) {
                        mvpView.getDataFail(msg);
                    }

                    @Override
                    public void onCompleted() {
                        mvpView.hideLoading();
                    }
                }));
    }

}
```

### apiStores.loadData
是不是很简单，关于Retrofit配置，详见源码AppClient。
```java
public interface ApiStores {
    //baseUrl
    String API_SERVER_URL = "http://www.weather.com.cn/";

    //加载天气
    @GET("adat/sk/{cityId}.html")
    Observable<MainModel> loadData(@Path("cityId") String cityId);
}

```

### RxJava回调方法
这里onError，写了如果网络请求用httpcode来判断。当然可以不要。
```java
public class SubscriberCallBack<T> extends Subscriber<T> {
    private ApiCallback<T> apiCallback;

    public SubscriberCallBack(ApiCallback<T> apiCallback) {
        this.apiCallback = apiCallback;
    }

    @Override
    public void onCompleted() {
        apiCallback.onCompleted();
    }

    @Override
    public void onError(Throwable e) {
        e.printStackTrace();
        if (e instanceof HttpException) {
            HttpException httpException = (HttpException) e;
            //httpException.response().errorBody().string()
            int code = httpException.code();
            String msg = httpException.getMessage();
            if (code == 504) {
                msg = "网络不给力";
            }
            apiCallback.onFailure(code, msg);
        } else {
            apiCallback.onFailure(0, e.getMessage());
        }
        apiCallback.onCompleted();
    }

    @Override
    public void onNext(T t) {
        apiCallback.onSuccess(t);
    }
}
```

### BasePresenter
再来看看BasePresenter，这里做了Presenter初始化和销毁(包括RXjava取消注册)，调用在MvpActivity。
```java
public class BasePresenter<V> implements Presenter<V> {
    public V mvpView;
    public ApiStores apiStores = AppClient.retrofit().create(ApiStores.class);
    private CompositeSubscription mCompositeSubscription;

    @Override
    public void attachView(V mvpView) {
        this.mvpView = mvpView;
    }

    @Override
    public void detachView() {
        this.mvpView = null;
        onUnsubscribe();
    }

    //RXjava取消注册，以避免内存泄露
    public void onUnsubscribe() {
        if (mCompositeSubscription != null && mCompositeSubscription.hasSubscriptions()) {
            mCompositeSubscription.unsubscribe();
        }
    }

    public void addSubscription(Observable observable, Subscriber subscriber) {
        if (mCompositeSubscription == null) {
            mCompositeSubscription = new CompositeSubscription();
        }
        mCompositeSubscription.add(observable
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(subscriber));
    }
}
```

# 源码地址
[https://github.com/WuXiaolong/AndroidMVPSample](https://github.com/WuXiaolong/AndroidMVPSample)

# 总结
三者结合使用，重点还是对MVP的优化，Retrofit只贴出最简单的（后续会写Retrofit详情使用），Rxjava可能我是对它认识尚浅，实际运用最多还是RxBus。

# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)