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
        mMainPresenter = new MainPresenter(this);       
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
    public MainPresenter(MainView view){
	attachView(view);
	}
	
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

# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
