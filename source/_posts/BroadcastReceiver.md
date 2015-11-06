title: Android之BroadcastReceiver
date: 2015-11-03 08:54:41
tags: BroadcastReceiver
category: Android
---
## 什么是BroadcastReceiver？

　　BroadcastReceiver，广播接收者，它是一个系统全局的监听器，用于监听系统全局的Broadcast消息，所以它可以很方便的进行系统组件之间的通信。

　　BroadcastReceiver属于系统级的监听器，它拥有自己的进程，只要存在与之匹配的Broadcast被以Intent的形式发送出来，BroadcastReceiver就会被激活。

　　虽然同属Android的四大组件，BroadcastReceiver也有自己独立的声明周期，但是和Activity、Service又不同。当在系统注册一个BroadcastReceiver之后，每次系统以一个Intent的形式发布Broadcast的时候，系统都会创建与之对应的BroadcastReceiver广播接收者实例，并自动触发它的onReceive()方法，当onReceive()方法被执行完成之后，BroadcastReceiver的实例就会被销毁。虽然它独自享用一个单独的进程，但也不是没有限制的，如果BroadcastReceiver.onReceive()方法不能在10秒内执行完成，Android系统就会认为该BroadcastReceiver对象无响应，然后弹出ANR（Application No Response）对话框，所以不要在BroadcastReceiver.onReceive()方法内执行一些耗时的操作。

　　如果需要根据广播内容完成一些耗时的操作，一般考虑通过Intent启动一个Service来完成该操作，而不应该在BroadcastReceiver中开启一个新线程完成耗时的操作，因为BroadcastReceiver本身的生命周期很短，可能出现的情况是子线程还没有结束，BroadcastReceiver就已经退出的情况，而如果BroadcastReceiver所在的进程结束了，该线程就会被标记为一个空线程，根据Android的内存管理策略，在系统内存紧张的时候，会按照优先级，结束优先级低的线程，而空线程无异是优先级最低的，这样就可能导致BroadcastReceiver启动的子线程不能执行完成。
<!--more-->
## BroadcastReceiver的种类
* sendBroadcast()：发送普通广播。
* sendOrderedBroadcast()：发送有序广播。

## 如何使用BroadcastReceiver
清单文件进行注册
```js
 <receiver android:name=".HelloBroadcastReceiver">
            <intent-filter android:priority="100">
                <action android:name="com.wuxiaolong.apksample.HelloBroadcastReceiver" />
            </intent-filter>
            </receiver>
```
HelloBroadcastReceiver
```java
public class HelloBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        // 判断当前接收到的Broadcast的action
        Log.d("wxl", "intent.getAction()====" + intent.getAction());
        if (intent.getAction().equals("com.wuxiaolong.apksample.HelloBroadcastReceiver")) {
            Toast.makeText(context,
                    "接收到Broadcast，消息为：" + intent.getStringExtra("msg"),
                    Toast.LENGTH_SHORT).show();
        }

    }
}
```
发送Broadcast
```java
Intent broadcast = new Intent();                
broadcast.setAction("com.wuxiaolong.apksample.HelloBroadcastReceiver");       
sendBroadcast(broadcast);
```
## 附录
[Android--广播BroadcastReceiver](http://www.cnblogs.com/plokmju/p/android_BroadcastReceiver.html)
[Android--拦截系统BroadcastReceiver](http://www.cnblogs.com/plokmju/p/android_SystemBroadcastReceriver.html)