title: Android基于XMPP开发（三）【注册、登录、修改密码、修改头像】
date: 2016-03-01 19:46:13
tags: [XMPP,Smack,openfire]
category: XMPP
---
> [Android基于XMPP开发（一）【openfire服务器配置】](http://wuxiaolong.me/2016/02/24/xmpp1/)
> [Android基于XMPP开发（二）【连接服务器】](http://wuxiaolong.me/2016/02/25/xmpp2/)

本篇将涉及常用的账号管理，包括：注册、登录、修改密码、修改头像。
<!-- more -->
# 注册
```
 public void register(final String userName, final String password) {
        new Thread(new Runnable() {
            @Override
            public void run() {

                String registerMsg;
                try {
                    if (!mXMPPTCPConnection.isConnected())
                        mXMPPTCPConnection.connect();
                } catch (Exception e) {
                    e.printStackTrace();
                    mXMPPTCPConnection.disconnect();
                }
                
                Log.i("wxl", "XMPPService connected=" + mXMPPTCPConnection.isConnected());
                if (mXMPPTCPConnection.isConnected()) {
                    String serviceName = mXMPPTCPConnection.getServiceName();
                    try {
                        AccountManager accountManager = AccountManager.getInstance(mXMPPTCPConnection);
                        Log.i("wxl", "Register supportsAccountCreation=" + accountManager.supportsAccountCreation());
                        if (accountManager.supportsAccountCreation()) {
                            accountManager.createAccount(userName + "@" + serviceName, password);
                            Log.i(TAG, "注册成功");
                            registerMsg = "注册成功";
                        } else {
                            Log.i(TAG, "服务端不能注册");
                            registerMsg = "服务端不能注册";
                        }

                    } catch (Exception e) {
                        e.printStackTrace();//
                        Log.i(TAG, "注册异常=" + e.getMessage());
                        registerMsg = "注册异常=" + e.getMessage();
                    }
                } else {
                    Log.i(TAG, "connect failed");
                    registerMsg = "connect failed";
                }
                mXMPPClickListener.register(registerMsg);
            }
        }).start();
    }
```


# 登录
```
 public void login(final String userName, final String password) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                String loginMsg;
                try {
                    if (!mXMPPTCPConnection.isConnected())
                        mXMPPTCPConnection.connect();
                } catch (Exception e) {
                    e.printStackTrace();
                    mXMPPTCPConnection.disconnect();
                }
               
                Log.i("wxl", "XMPPService login connected=" + mXMPPTCPConnection.isConnected());
                if (mXMPPTCPConnection.isConnected()) {
                    try {
                        mXMPPTCPConnection.login(userName, password);

                        if (mXMPPTCPConnection.isAuthenticated()) {
                            Log.i(TAG, "登录成功");
                            loginMsg = "登录成功";
                        } else {
                            Log.i(TAG, "登录失败");
                            loginMsg = "登录失败";
                        }

                    } catch (Exception e) {
                        e.printStackTrace();//
                        Log.i(TAG, "登录异常=" + e.getMessage());
                        loginMsg = "登录异常=" + e.getMessage();
                    }
                } else {
                    Log.i(TAG, "connect failed");
                    loginMsg = "connect failed";
                }

                mXMPPClickListener.login(loginMsg);
            }
        }).start();
    }
```

# 修改密码
修改密码必须在登录状态下
```
  public void changePassword(final String newPassword) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                String changePasswordMsg;

                try {
                    if (!mXMPPTCPConnection.isConnected())
                        mXMPPTCPConnection.connect();
                } catch (Exception e) {
                    e.printStackTrace();
                    mXMPPTCPConnection.disconnect();
                }
                Log.i("wxl", "XMPPService connected=" + mXMPPTCPConnection.isConnected());
                if (mXMPPTCPConnection.isConnected()) {
                    try {

                        if (mXMPPTCPConnection.isAuthenticated()) {
                            AccountManager accountManager = AccountManager.getInstance(mXMPPTCPConnection);
                            accountManager.changePassword(newPassword);
                            Log.i(TAG, "修改密码成功");
                            changePasswordMsg = "修改密码成功";
                        } else {
                            Log.i(TAG, "请先登录");
                            changePasswordMsg = "请先登录";
                        }

                    } catch (Exception e) {
                        e.printStackTrace();//
                        Log.i(TAG, "修改密码异常=" + e.getMessage());
                        changePasswordMsg = "修改密码异常=" + e.getMessage();
                    }
                } else {
                    Log.i(TAG, "connect failed");
                    changePasswordMsg = "connect failed";
                }
                mXMPPClickListener.register(changePasswordMsg);
            }
        }).start();
    }

```
# 修改头像
```
  public void setAvatar(final Bitmap bitmap) {
        new Thread(new Runnable() {
            @Override
            public void run() {


                try {
                    if (!mXMPPTCPConnection.isConnected())
                        mXMPPTCPConnection.connect();
                } catch (Exception e) {
                    e.printStackTrace();
                    mXMPPTCPConnection.disconnect();
                }
                Log.i("wxl", "XMPPService connected=" + mXMPPTCPConnection.isConnected());
                if (mXMPPTCPConnection.isConnected()) {
                    try {
                        if (mXMPPTCPConnection.isAuthenticated()) {
                            VCardManager vCardManager = VCardManager.getInstanceFor(mXMPPTCPConnection);
                            VCard vCard = vCardManager.loadVCard();
                            byte[] bytes = bitmapToByte(bitmap);
                            String encodedImage = Base64.encodeToString(bytes, Base64.DEFAULT);
                            vCard.setAvatar(bytes);
                            vCardManager.saveVCard(vCard);
                            mXMPPClickListener.setAvatar("修改头像成功");
                        } else {
                            mXMPPClickListener.setAvatar("请先登录");
                        }

                    } catch (Exception e) {
                        e.printStackTrace();//
                        mXMPPClickListener.setAvatar("修改头像异常=" + e.getMessage());
                    }
                } else {
                    mXMPPClickListener.setAvatar("connect failed");
                }
            }
        }).start();
    }

```
bitmapToByte
```
    private byte[] bitmapToByte(Bitmap bitmap) {
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        bitmap.compress(Bitmap.CompressFormat.PNG, 100, baos);
        return baos.toByteArray();
    }
```

# 源码地址
[https://github.com/WuXiaolong/WoChat](https://github.com/WuXiaolong/WoChat)

# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_AndroidProgrammr.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)