title: 解决Android视频锁屏还在继续播放
date: 2014-11-24
categories: [Android]
tags: [Android,Video]
---
###一、锁屏
T_T被整哭了，我明知道锁屏走了onDestroy()方法，没有着手这个，反而去监听锁屏，还是`太菜`，`基础不牢`。

看日志发现，主Activity先onDestroy()了，随即又onCreate()，原因如下：

视频本身是横屏，手机界面是竖屏，锁屏操作存在一个横屏转竖屏的过程，这个过程如果不加android:configChanges="orientation",
Activity会自动重新走一遍它的生命周期，即先销毁再重新创建，加上后只会调用onConfigurationChanged。`哎，我知道这个，怎么没有想到这点啊`。

另外如果你的target > 13时，还必须得加上“ScreenSize”。

<!-- more -->

```java
 <activity
  android:name="com.xiaomolong.ufosay.ui.VideoPlayActivity"
  android:configChanges="keyboardHidden|orientation|screenSize"
  android:label="PlayingActivity"
  android:screenOrientation="landscape"
  android:theme="@android:style/Theme.Holo.Light.NoActionBar.Fullscreen" >
  </activity>
```

###二、记录视频进度

视频播放点击Home建和锁屏时需要保存播放的进度，因此有必要了解下onSaveInstanceState和onRestoreInstanceState触发的时机

* 1、onSaveInstanceState

(1)、当用户按下HOME键时。

这是显而易见的，系统不知道你按下HOME后要运行多少其他的程序，自然也不知道activity A是否会被销毁，故系统会调用onSaveInstanceState，让用户有机会保存某些非永久性的数据。以下几种情况的分析都遵循该原则

(2)、长按HOME键，选择运行其他的程序时。

(3)、按下电源按键（关闭屏幕显示）时。

(4)、从activity A中启动一个新的activity时。

(5)、屏幕方向切换时，例如从竖屏切换到横屏时。

在屏幕切换之前，系统会销毁activity A，在屏幕切换之后系统又会自动地创建activity A，所以onSaveInstanceState一定会被执行

* 2、onRestoreInstanceState

onRestoreInstanceState被调用的前提是，activity A“确实”被系统销毁了，而如果仅仅是停留在有这种可能性的情况下，则该方法不会被调用，例如，当正在显示activity A的时候，用户按下HOME键回到主界面，然后用户紧接着又返回到activity A，这种情况下activity A一般不会因为内存的原因被系统销毁，故activity A的onRestoreInstanceState方法不会被执行

另外，onRestoreInstanceState的bundle参数也会传递到onCreate方法中，你也可以选择在onCreate方法中做数据还原。

```java
  /**
	 * 记录播放位置
	 */
	private int mLastPos = 0;
  protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.video_play);
		if (savedInstanceState != null) {
		mLastPos = savedInstanceState.getInt("LastPosition");
		}
	}
	protected void onRestoreInstanceState(Bundle savedInstanceState) {
		super.onRestoreInstanceState(savedInstanceState);
		Log.d("wxl", "onRestoreInstanceState start");
		if (savedInstanceState != null) {
			mLastPos = savedInstanceState.getInt("LastPosition");
			Log.d("wxl", "onRestoreInstanceState " + mLastPos);
		}
	}

	@Override
	protected void onSaveInstanceState(Bundle outState) {
		super.onSaveInstanceState(outState);
		Log.d("wxl", "onSaveInstanceState start");
		outState.putInt("LastPosition", mLastPos);
	}
```

###三、附录

[Android锁屏后主Activity的onDestroy方法被调用](http://blog.csdn.net/terrantian/article/details/18670381)

[onSaveInstanceState和onRestoreInstanceState触发的时机](http://android.blog.51cto.com/268543/634646/)

[Android 监听屏幕锁屏,用户解锁](http://www.cnblogs.com/a284628487/p/3345317.html)
