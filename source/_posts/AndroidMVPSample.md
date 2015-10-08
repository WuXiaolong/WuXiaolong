title: Android MVP 实例
date: 2015-09-23 16:28:22
tags: [Android,MVP]
category: Android
---
# Android MVP 实例
简单的请求天气功能，演示Android MVP是如何使用的

# 效果预览
![](https://github.com/WuXiaolong/AndroidMVPSample/raw/master/screenshots.png)
<!--more-->
# 准备
gradle文件
```java
 compile 'com.loopj.android:android-async-http:1.4.9'
```
说明：请求网络使用async-http

MainActivity
```java
public class MainActivity extends AppCompatActivity implements MainView {
    ProgressBar mProgressBar;
    TextView text;
    MainPresenter mMainPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();

    }

    private void initView() {
        text = (TextView) findViewById(R.id.text);
        mProgressBar = (ProgressBar) findViewById(R.id.mProgressBar);
        mMainPresenter = new MainPresenter();
        mMainPresenter.attachView(this);
        mMainPresenter.loadData();
    }

    @Override
    protected void onDestroy() {
        mMainPresenter.detachView();
        super.onDestroy();
    }

    @Override
    public void showData(MainModel mainModel) {
        text.setText("城市：" + mainModel.getCity()
                + "\n风向：" + mainModel.getWd()
                + "\n风级：" + mainModel.getWs()
                + "\n发布时间：" + mainModel.getTime());
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

# MVP之M
MainModel 
```java
public class MainModel {
    String city;
    String wd;
    String ws;
    String time;

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getWd() {
        return wd;
    }

    public void setWd(String wd) {
        this.wd = wd;
    }

    public String getWs() {
        return ws;
    }

    public void setWs(String ws) {
        this.ws = ws;
    }

    public String getTime() {
        return time;
    }

    public void setTime(String time) {
        this.time = time;
    }
}
```



# MVP之P

MainPresenter.java 

```java

//业务具体处理
public class MainPresenter implements Presenter<MainView> {
    private MainView mMainView;

    @Override
    public void attachView(MainView view) {
        this.mMainView = view;
    }

    public void loadData() {
        mMainView.showProgress();
        AsyncHttpClient asyncHttpClient = new AsyncHttpClient();
        asyncHttpClient.get("http://www.weather.com.cn/adat/sk/101010100.html", new JsonHttpResponseHandler() {
            @Override
            public void onSuccess(int statusCode, Header[] headers, JSONObject response) {
                super.onSuccess(statusCode, headers, response);
                try {
                    MainModel mainModel = new MainModel();
                    JSONObject weatherinfo = response.getJSONObject("weatherinfo");
                    mainModel.setCity(weatherinfo.getString("city"));
                    mainModel.setWd(weatherinfo.getString("WD"));
                    mainModel.setWs(weatherinfo.getString("WS"));
                    mainModel.setTime(weatherinfo.getString("time"));
                    mMainView.showData(mainModel);
                    mMainView.hideProgress();
                } catch (JSONException e) {
                    e.printStackTrace();
                }

            }

            @Override
            public void onFailure(int statusCode, Header[] headers, Throwable throwable, JSONObject errorResponse) {
                super.onFailure(statusCode, headers, throwable, errorResponse);
                mMainView.hideProgress();
            }
        });
    }

    @Override
    public void detachView() {
        this.mMainView = null;
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

# MVP之V

MainView.java

```java

//处理业务需要哪些方法
public interface MainView {
    void showData(MainModel mainModel);

    void showProgress();

    void hideProgress();
}

``` 

# 源码地址
[https://github.com/WuXiaolong/AndroidMVPSample](https://github.com/WuXiaolong/AndroidMVPSample)

# PS
使用mvp时，Activity和Fragment变成了view层的一部分而把绝大多数工作代理给了presenter。每一个Activity都有一个相应的presenter来处理数据进而获取model。