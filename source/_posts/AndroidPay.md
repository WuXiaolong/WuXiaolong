---
title: Android 接入微信支付宝支付
date: 2016-11-22 20:32:07
tags: AndroidPay
category: Android
---

按照惯例，首先通读一遍官网文档。

# 微信支付
## 前期准备
### 微信开放平台
注册账号，创建应用，开通微信支付，这些大家都会的。

### 微信商户平台
<!--more-->
APP支付文档
https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=8_1 ，如果仅按照文档来，保证每次支付结果都是-1，对，只有-1，没有任何其他信息，不得不说，坑比。

APP支付时序图
大概了解下支付流程：
![](https://pay.weixin.qq.com/wiki/doc/api/img/chapter8_3_1.png)


## 接入SDK
### 引入libs
SDK下载：https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=11_1 ，将libammsdk.jar放到libs文件夹下

### 配置debug签名
创建应用需要填写应用签名，配置在debug下直接用的正式的key，这样就不用每次打包才能调起微信客户端
```java
signingConfigs {
        debug {
            storeFile file("你的keystore路径")
            storePassword "xxx"
            keyAlias "xxx"
            keyPassword "xxx"
        }

        release {
            storeFile file("你的keystore路径")
            storePassword "xxx"
            keyAlias "xxx"
            keyPassword "xxx"
        }
}
```
### WXPayEntryActivity
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidPay1.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
这个**文档没有半点介绍**，WXPayEntryActivity是支付结束的回调，和微信分享WXEntryActivity类似，必须放在包名.wxapi下，页面可以自己写。

AndroidManifest.xml中注册
```xml
<!-- 微信支付 -->
<activity
    android:name=".wxapi.WXPayEntryActivity"
    android:exported="true"
    android:launchMode="singleTop"/>
```
网络权限注册不用说了。

### 支付接口调用
```java
/**
 * 微信支付
 * @param data接口返回
 */
private void weixinPay(String data) {
    // 通过WXAPIFactory工厂，获取IWXAPI的实例
    IWXAPI api = WXAPIFactory.createWXAPI(this, WEIXIN_APP_ID);
    // 将该app注册到微信
    api.registerApp(WEIXIN_APP_ID);
    LogUtil.d("data=" + data);
    try {
        JSONObject json = new JSONObject(data);
        PayReq req = new PayReq();
        req.appId = json.getString("appid");
        req.partnerId = json.getString("partnerid");
        req.prepayId = json.getString("prepayid");
        req.nonceStr = json.getString("noncestr");
        req.timeStamp = json.getString("timestamp");
        req.packageValue = json.getString("package");
        req.sign = json.getString("sign");
        req.extData = "app data"; // optional
        // 在支付之前，如果应用没有注册到微信，应该先调用IWXMsg.registerApp将应用注册到微信
        api.sendReq(req);
    } catch (JSONException e) {
        e.printStackTrace();
    }
}
```
为了安全，APP端调起支付的参数需要商户后台系统提供接口返回，参数说明文档：https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_12&index=2

## 问题
以为这样就可以调起微信客户端支付了，没有想到一直返回支付结果为-1，没有多余的信息提示，不亏是微信，Android代码就是简洁！我们看到文档返回-1的提示“可能的原因：签名错误、未注册APPID、项目设置APPID不正确、注册的APPID与设置的不匹配、其他异常等。”，我反复检查签名、APPID均没有错误，后台也确定sign信息正确，哎，我表示很无奈，iOS也调用了，他们提示”支付场景非法“，原来是由于后台之前做web支付，trade_type没有改成APP，改了我这边就能正常调起了。
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidPay2.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

# 支付宝接入
## 前期准备
注册账号，创建应用，开通支付功能，这些也不说了。
### 接入文档
https://doc.open.alipay.com/docs/doc.htm?spm=a219a.7386797.0.0.Eky59h&treeId=59&articleId=103563&docType=1

### 支付交互流程
![](https://img.alicdn.com/top/i1/LB1d7GlMVXXXXbTXFXXXXXXXXXX)

## 接入SDK
SDK下载：
https://doc.open.alipay.com/docs/doc.htm?spm=a219a.7629140.0.0.Q5gnm8&treeId=193&articleId=104509&docType=1 ，
将alipaySdk-xxxxxxxx.jar包放到libs文件夹下

### AndroidManifest.xml
添加权限
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```
添加声明
```xml
<!-- 支付宝 alipay sdk begin -->
<activity
    android:name="com.alipay.sdk.app.H5PayActivity"
    android:configChanges="orientation|keyboardHidden|navigation"
    android:exported="false"
    android:screenOrientation="behind">
</activity>
<activity
    android:name="com.alipay.sdk.auth.AuthActivity"
    android:configChanges="orientation|keyboardHidden|navigation"
    android:exported="false"
    android:screenOrientation="behind">
</activity>
```

### 支付接口调用
支付行为需要在独立的非ui线程中执行，代码如下：
```java
private Handler mHandler = new Handler() {
        @SuppressWarnings("unused")
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case SDK_PAY_FLAG: {
                    @SuppressWarnings("unchecked")
                    PayResult payResult = new PayResult((Map<String, String>) msg.obj);
                    /**
                     对于支付结果，请商户依赖服务端的异步通知结果。同步通知结果，仅作为支付结束的通知。
                     */
                    String resultInfo = payResult.getResult();// 同步返回需要验证的信息
                    String resultStatus = payResult.getResultStatus();
                    // 判断resultStatus 为9000则代表支付成功
                    if (TextUtils.equals(resultStatus, "9000")) {
                        // 该笔订单是否真实支付成功，需要依赖服务端的异步通知。
                        Toast.makeText(mActivity, "支付成功", Toast.LENGTH_SHORT).show();
                    } else {
                        // 该笔订单真实的支付结果，需要依赖服务端的异步通知。
                        Toast.makeText(mActivity, payResult.getMemo(), Toast.LENGTH_SHORT).show();
                    }
                    break;
                }

                default:
                    break;
            }
        }

        ;
    };

    /**
     * 支付宝
     */
    private void alipay(final String orderInfo) {
        Runnable payRunnable = new Runnable() {

            @Override
            public void run() {
                PayTask alipay = new PayTask(mActivity);
                Map<String, String> result = alipay.payV2(orderInfo, true);
                Log.i("msp", result.toString());

                Message msg = new Message();
                msg.what = SDK_PAY_FLAG;
                msg.obj = result;
                mHandler.sendMessage(msg);
            }
        };

        Thread payThread = new Thread(payRunnable);
        payThread.start();
    }
```
同样为了安全，这里orderInfo是后台接口返回，参数说明文档：
https://doc.open.alipay.com/docs/doc.htm?spm=a219a.7629140.0.0.DwA283&treeId=59&articleId=103663&docType=1

这样就over了，相比于微信支付，简单的不能再简单。
