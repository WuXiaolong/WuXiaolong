title: Android基于XMPP开发（二）【连接服务器】
date: 2016-02-25 19:34:53
tags: [XMPP,Smack,openfire]
category: XMPP
---
# 准备阶段
砍柴不误磨刀工，欲XMPP开发，先引入Smack，具体见[https://github.com/igniterealtime/Smack](https://github.com/igniterealtime/Smack)，引入方式如下：
app/build.gradle
```
repositories {
    maven {
        url 'https://oss.sonatype.org/content/repositories/snapshots'
    }
    mavenCentral()
}
dependencies {
compile "org.igniterealtime.smack:smack-android-extensions:4.1.6"
compile 'org.igniterealtime.smack:smack-tcp:4.1.6'
compile 'org.igniterealtime.smack:smack-experimental:4.1.6'
}
```
<!-- more -->

# 初始化连接
这里为了更好的演示，我选择是XMPP公共服务器：yax.im
```
  private static final String TAG = "wxl";
  public final static String HOST = "yax.im";
  private static final String SERVICE_NAME = "yax.im"; //sunchunlei.local labsun.com
  public final static int PORT = 5222;
  public static AbstractXMPPConnection mXMPPTCPConnection;
  private static SSLContext mSSLContext;
  public AbstractXMPPConnection initXMPPTCPConnection() {
        SmackConfiguration.DEBUG = true;
        try {
            mSSLContext = SSLContext.getInstance("TLS");
            mSSLContext.init(null, new TrustManager[]{new MyTrustManager()}, null);
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("connecting failed", e);
        }

        XMPPTCPConnectionConfiguration.Builder builder = XMPPTCPConnectionConfiguration.builder();
//        builder.setUsernameAndPassword(USER_NAME, PASSWORD);
        builder.setHost(HOST);
        builder.setServiceName(SERVICE_NAME);//此处只能使用域名或PC机器名称
        builder.setPort(PORT);
        builder.setCompressionEnabled(false);//连接套将使用流压缩。
//        builder.setConnectTimeout(5000);
//        builder.setDebuggerEnabled(true);
//        builder.setSendPresence(true);//上线通知系统
        builder.setSecurityMode(ConnectionConfiguration.SecurityMode.required);//安全模式
        builder.setCustomSSLContext(mSSLContext);////https不验证证书方式（信任所有证书）
        SASLAuthentication.unBlacklistSASLMechanism("PLAIN");
        SASLAuthentication.unBlacklistSASLMechanism("DIGEST-MD5");
        mXMPPTCPConnection = new XMPPTCPConnection(builder.build());
        return mXMPPTCPConnection;
    }
```
MyTrustManager 
```
 private class MyTrustManager implements X509TrustManager {
        @Override
        public void checkClientTrusted(X509Certificate[] x509Certificates, String s)
                throws CertificateException {
            Log.i(TAG, "checkClientTrusted:" + s);
        }

        @Override
        public void checkServerTrusted(X509Certificate[] x509Certificates, String s)
                throws CertificateException {
            Log.i(TAG, "checkServerTrusted:" + s);
        }

        @Override
        public X509Certificate[] getAcceptedIssuers() {
            Log.i(TAG, "getAcceptedIssuers");
            return new X509Certificate[0];
        }

    }
```
# Connect to the server
异步处理，不然会android.os.NetworkOnMainThreadException

```
public void connect() {
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Message message = new Message();
                    message.what = AppConstants.CONNECT;
                    Log.i(TAG, "connect front");
                    if (mXMPPTCPConnection.isConnected()) {
                        Log.i(TAG, "connect successfull");
                        message.obj = "connect successfull";
                    } else {
                        mXMPPTCPConnection.connect();
                        if (mXMPPTCPConnection.isConnected()) {
                            message.obj = "connect successfull";
                        } else {
                            message.obj = "connect failed";
                        }

                    }

                    Log.i(TAG, "connect end=" + mXMPPTCPConnection.isConnected());
                    mXMPPHandler.sendMessage(message);
                } catch (Exception e) {
                    e.printStackTrace();
                    Log.i(TAG, "connect e=" + e.getMessage());
                }
            }
        }

        );
        thread.start();
    }
```
XMPPHandler
```
public class XMPPHandler extends Handler {
    public XMPPClickListener mXMPPClickListener;
    private static final String TAG = "wxl";

    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        String text = (String) msg.obj;
        switch (msg.what) {
            case AppConstants.CONNECT:
                mXMPPClickListener.connect(text);
                break;
            case AppConstants.LOGIN:

                break;
            case AppConstants.REGISTER:

                break;
        }
    }


    public void setXMPPClickListener(XMPPClickListener xmppClickListener) {
        Log.i(TAG, "setXMPPClickListener=" + xmppClickListener);
        this.mXMPPClickListener = xmppClickListener;
    }
}
```
XMPPClickListener 
```
public interface XMPPClickListener {
    void connect(String msg);
}

```
MainActivity
```
public class MainActivity extends AppCompatActivity implements View.OnClickListener, XMPPClickListener {
    private XMPPService mXMPPService;
    private XMPPHandler mXMPPHandler = new XMPPHandler();
    private ProgressDialog mProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mXMPPService = new XMPPService(mXMPPHandler);
        mXMPPHandler.setXMPPClickListener(this);
        mXMPPService.initXMPPTCPConnection();
        mProgressDialog = new ProgressDialog(MainActivity.this);
        mProgressDialog.setMessage("加载中");
        findViewById(R.id.connect).setOnClickListener(this);
    }
    @Override
    public void onClick(View v) {
        progressDialogShow();
        switch (v.getId()) {
            case R.id.connect:
                mXMPPService.connect();
                break;
        }
    }

    @Override
    public void connect(String msg) {
        toastShow(msg);
    }

    private void toastShow(CharSequence text) {
        progressDialogDismiss();
        Toast.makeText(MainActivity.this, text, Toast.LENGTH_LONG).show();

    }


    private void progressDialogShow() {

        mProgressDialog.show();

    }

    private void progressDialogDismiss() {
        if (mProgressDialog.isShowing()) mProgressDialog.dismiss();

    }
}
```
# 源码地址
[https://github.com/WuXiaolong/WoChat](https://github.com/WuXiaolong/WoChat)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)