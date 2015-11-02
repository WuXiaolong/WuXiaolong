title: Android之Service
date: 2014-11-02 15:23:04
tags: Service
category: Android
---
## 使用清单文件声明服务
```js
<manifest ... >
  ...
  <application ... >
        <service android:name=".HelloService" />
      ...
  </application>
</manifest>
```
<!--more-->
## 管理服务生命周期
![](http://7q5c2h.com1.z0.glb.clouddn.com/service_lifecycle.png)
* 启动的服务
startService()->onCreate()->onStartCommand()->running->stopService()/stopSelf()->onDestroy()->stopped 其中，服务未运行时会调用一次onCreate()，运行时不调用。
* 绑定的服务
bindService()->onCreate()->onBind()->running->onUnbind()->onDestroy()->stopped

## 代码示例
xml
```js
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/start_service"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Start Service" />

    <Button
        android:id="@+id/stop_service"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:text="Stop Service" />
    <Button
        android:id="@+id/bind_service"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:text="Stop Service" />
    <Button
        android:id="@+id/unbind_service"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:text="Stop Service" />

</LinearLayout>  
```
java
```java
public class HelloServiceActivity extends AppCompatActivity {
    HelloService.HelloBinder mBinder;
    //Service和Activity通信
    private ServiceConnection serviceConnection = new ServiceConnection() {

        @Override
        public void onServiceDisconnected(ComponentName name) {
        }

        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            mBinder = (HelloService.HelloBinder) service;
            mBinder.startDownload();
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_hello_service);
        findViewById(R.id.start_service).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(getApplicationContext(), HelloService.class);
                startService(intent);
            }
        });
        findViewById(R.id.stop_service).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(getApplicationContext(), HelloService.class);
                stopService(intent);
            }
        });
        findViewById(R.id.bind_service).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //调用者与服务绑定在了一起，调用者一旦退出，服务也就终止
                Intent intent = new Intent(getApplicationContext(), HelloService.class);
                bindService(intent, serviceConnection, BIND_AUTO_CREATE);
            }
        });
        findViewById(R.id.unbind_service).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //让Service和Activity解除关联
                unbindService(serviceConnection);
            }
        });

    }

}
```
HelloService
```java
public class HelloService extends Service {
    int mStartMode;       // indicates how to behave if the service is killed
    //    IBinder mBinder;      // interface for clients that bind
    boolean mAllowRebind; // indicates whether onRebind should be used
    private HelloBinder mBinder = new HelloBinder();

    @Override
    public void onCreate() {
        // The service is being created
        Log.d("wxl", "HelloService onCreate");
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // The service is starting, due to a call to startService()
        Log.d("wxl", "HelloService onStartCommand");
        flags = START_STICKY;
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public IBinder onBind(Intent intent) {
        // A client is binding to the service with bindService()
        Log.d("wxl", "HelloService onBind");
        return mBinder;
    }

    @Override
    public boolean onUnbind(Intent intent) {
        // All clients have unbound with unbindService()
        Log.d("wxl", "HelloService onUnbind");
        return mAllowRebind;
    }

    @Override
    public void onRebind(Intent intent) {
        // A client is binding to the service with bindService(),
        // after onUnbind() has already been called
        Log.d("wxl", "HelloService onRebind");
    }

    public class HelloBinder extends Binder {

        public void startDownload() {
            Log.d("wxl", "HelloService startDownload() executed");
            // 执行具体的下载任务
        }

    }

    @Override
    public void onDestroy() {
        // The service is no longer used and is being destroyed
        Log.d("wxl", "HelloService onDestroy");
        Intent intent = new Intent(this, HelloService.class);
        startService(intent);
    }
}
```
## Service KILL问题
* settings 中stop service
onDestroy方法中，调用startService进行Service的重启。
* settings中force stop 应用
未解决
* 借助第三方应用kill掉running task
未解决

## 附录
[官方Service API指南](http://developer.android.com/intl/zh-cn/guide/components/services.html)
[Android Service完全解析，关于服务你所需知道的一切(上)](http://blog.csdn.net/guolin_blog/article/details/11952435)
[Service服务详解以及如何使service服务不被杀死](http://www.cnblogs.com/rossoneri/p/4530216.html)