title: Android MVP 实例
date: 2015-09-23 16:28:22
tags: [MVP]
category: Android
---
# Android MVP 实例
简单的请求天气功能，演示Android MVP是如何使用的

# 效果预览
![](https://github.com/WuXiaolong/AndroidMVPSample/raw/master/screenshots.png)
<!--more-->

# 准备
## MVP流程
![](http://7q5c2h.com1.z0.glb.clouddn.com/androidmvpsample2.png)
说明：
步骤1：UI实现View方法，引用Presenter
步骤2：Presenter调用Model，走Model具体逻辑
步骤3：Model逻辑实现，回调Presenter方法
步骤4：Presenter回调View，即回到UI，回调View方法

## gradle文件
```java
 compile 'com.loopj.android:android-async-http:1.4.9'
```
说明：请求网络使用async-http

## 目录结构
![](http://7q5c2h.com1.z0.glb.clouddn.com/androidmvpsample3.png)

# MVP之V

MainView.java

```java
/**
 * Created by WuXiaolong on 2015/9/23.
 * 处理业务需要哪些方法
 */
public interface MainView {
    void showData(MainModelBean mainModelBean);

    void showProgress();

    void hideProgress();
}
```

MainActivity

```java
/**
 * Created by WuXiaolong on 2015/9/23.
 * 由Activity/Fragment实现View里方法，包含一个Presenter的引用
 */
public class MainActivity extends AppCompatActivity implements MainView {
    private ProgressBar mProgressBar;
    private TextView text;
    private MainPresenter mMainPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();

    }

    private void initView() {
        text = (TextView) findViewById(R.id.text);
        mProgressBar = (ProgressBar) findViewById(R.id.mProgressBar);
        mMainPresenter = new MainPresenter(this);
        //制造延迟效果
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                mMainPresenter.loadData();
            }
        }, 2000);

    }

    @Override
    protected void onDestroy() {
        mMainPresenter.detachView();
        super.onDestroy();
    }

    @Override
    public void showData(MainModelBean mainModelBean) {
        String showData = getResources().getString(R.string.city) + mainModelBean.getCity()
                + getResources().getString(R.string.wd) + mainModelBean.getWd()
                + getResources().getString(R.string.ws) + mainModelBean.getWs()
                + getResources().getString(R.string.time) + mainModelBean.getTime();
        text.setText(showData);
    }


    @Override
    public void showProgress() {
        mProgressBar.setVisibility(View.VISIBLE);
    }

    @Override
    public void hideProgress() {
        mProgressBar.setVisibility(View.GONE);
    }


}

```

# MVP之P

MainPresenter.java 
```java
/**
 * Created by WuXiaolong on 2015/9/23.
 * View和Model的桥梁，它从Model层检索数据后，返回给View层
 */
public class MainPresenter implements Presenter<MainView>, IMainPresenter {
    private MainView mMainView;
    private MainModel mMainModel;

    public MainPresenter(MainView view) {
        attachView(view);
        mMainModel = new MainModel(this);
    }

    @Override
    public void attachView(MainView view) {
        this.mMainView = view;
    }

    @Override
    public void detachView() {
        this.mMainView = null;
    }

    public void loadData() {
        mMainView.showProgress();
        mMainModel.loadData();
    }


    @Override
    public void loadDataSuccess(MainModelBean mainModelBean) {
        mMainView.showData(mainModelBean);
        mMainView.hideProgress();
    }

    @Override
    public void loadDataFailure() {
        mMainView.hideProgress();
    }
}

```

Presenter
```java
public interface Presenter<V> {

    void attachView(V view);

    void detachView();

}
```
IMainPresenter
```java
/**
 * Created by WuXiaolong on 2015/9/23.
 * 此接口作用是连接Model
 */
public interface IMainPresenter {
    void loadDataSuccess(MainModelBean mainModelBean);

    void loadDataFailure();
}

```
# MVP之M
MainModel 
```java
/**
 * Created by WuXiaolong on 2015/9/23.
 * 业务具体处理，包括负责存储、检索、操纵数据等
 */
public class MainModel {
    IMainPresenter mIMainPresenter;

    public MainModel(IMainPresenter iMainPresenter) {
        this.mIMainPresenter = iMainPresenter;
    }

    public void loadData() {
        AsyncHttpClient asyncHttpClient = new AsyncHttpClient();
        asyncHttpClient.get("http://www.weather.com.cn/adat/sk/101010100.html", new JsonHttpResponseHandler() {
            @Override
            public void onSuccess(int statusCode, Header[] headers, JSONObject response) {
                super.onSuccess(statusCode, headers, response);
                try {
                    MainModelBean mainModelBean = new MainModelBean();
                    JSONObject weatherinfo = response.getJSONObject("weatherinfo");
                    mainModelBean.setCity(weatherinfo.getString("city"));
                    mainModelBean.setWd(weatherinfo.getString("WD"));
                    mainModelBean.setWs(weatherinfo.getString("WS"));
                    mainModelBean.setTime(weatherinfo.getString("time"));
                    mIMainPresenter.loadDataSuccess(mainModelBean);
                } catch (JSONException e) {
                    e.printStackTrace();
                }

            }

            @Override
            public void onFailure(int statusCode, Header[] headers, Throwable throwable, JSONObject errorResponse) {
                super.onFailure(statusCode, headers, throwable, errorResponse);
                mIMainPresenter.loadDataFailure();
            }
        });
    }


}
```
MainModelBean
```java
public class MainModelBean {
    private String city;
    private String wd;
    private String ws;
    private String time;
    //此处省略get和set方法    
}
```

# 源码地址
[https://github.com/WuXiaolong/AndroidMVPSample](https://github.com/WuXiaolong/AndroidMVPSample)

# 总结
## MVC模式
视图（View）：用户界面。
控制器（Controller）：业务逻辑
模型（Model）：数据保存
View 传送指令到 Controller
Controller 完成业务逻辑后，要求 Model 改变状态
Model 将新的数据发送到 View，用户得到反馈

## MVP模式
使用MVP时，Activity和Fragment变成了MVC模式中View层，Presenter相当于MVC模式中Controller层，处理业务逻辑。每一个Activity都有一个相应的presenter来处理数据进而获取model。

## MVVM模式
将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。唯一的区别是，它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。
