title: Activity生命周期和启动模式
date: 2015-11-01 19:57:34
tags: Activity
category: Android
---
# Activity生命周期
```java
public class LifeCycleActivity extends AppCompatActivity {
    static final String TAG = "wxl";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.d(TAG, "onCreate");
        setContentView(R.layout.activity_life_cycle);
    }
    
  public void click(View view) {
        startActivity(new Intent(this, StartModeActivity.class));
    }
    
    @Override
    public void onContentChanged() {
        super.onContentChanged();
        Log.d(TAG, "onContentChanged");
    }

    public void onStart() {
        super.onStart();
        Log.d(TAG, "onStart");
    }

    public void onRestart() {
        super.onRestart();
        Log.d(TAG, "onRestart");
    }

    public void onPostCreate(Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);
        Log.d(TAG, "onPostCreate");
    }

    @Override
    public void onResume() {
        super.onResume();
        Log.d(TAG, "onResume");
    }

    public void onPostResume() {
        super.onPostResume();
        Log.d(TAG, "onPostResume");
    }

    public void onPause() {
        super.onPause();
        Log.d(TAG, "onPause");
    }

    public void onStop() {
        super.onStop();
        Log.d(TAG, "onStop");
    }

    public void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy");
    }

    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        Log.d(TAG, "onConfigurationChanged");
    }

    public void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        Log.d(TAG, "onSaveInstanceState");
    }

    public void onRestoreInstanceState(Bundle outState) {
        super.onRestoreInstanceState(outState);
        Log.d(TAG, "onRestoreInstanceState");
    }
}
```
<!--more-->
## 正常启动 
onCreate --> onContentChanged --> onStart --> onPostCreate --> onResume --> onPostResume --> onPause --> onStop --> onDestroy

## onCreate、 onStart
onCreate方法会在第一次创建的时候执行，紧接着便会执行onStart方法

## onContentChanged
onContentChanged()是Activity中的一个回调方法
当Activity的布局改动时，即setContentView()或者addContentView()方法执行完毕时就会调用该方法， 例如，Activity中各种View的findViewById()方法都可以放到该方法中。

## onPostCreate、onPostResume
onPostCreate方法是指onCreate方法彻底执行完毕的回调，onPostResume类似，这两个方法官方说法是一般不会重写，现在知道的做法也就只有在使用ActionBarDrawerToggle的使用在onPostCreate需要在屏幕旋转时候等同步下状态，Google官方提供的一些实例就是如下做法：
```java
@Override
protected void onPostCreate(Bundle savedInstanceState) {
    super.onPostCreate(savedInstanceState);
    // Sync the toggle state after onRestoreInstanceState has occurred.
    mDrawerToggle.syncState();
}
```

## onResume
此时Activity处于Activity栈顶，处理用户交互。

## onPause、 onStop
onPause是在整个窗口被半遮盖或者半透明的时候会执行，Activity不会接受用户输入。而onStop则是在整个窗口被完全遮盖才会触发，此时Activity不可见，尽在后台运行。触发onStop的方法之前必定会触发onPause方法。

## onSaveInstanceState、 onRestoreInstanceState
onSaveInstanceState字面理解就是恢复实例的状态， 需要注意的是，onSaveInstanceState方法和onRestoreInstanceState方法“不一定”是成对的被调用的，onRestoreInstanceState被调用的前提是，activity A“确实”被系统销毁了，而如果仅仅是停留在有这种可能性的情况下，则该方法不会被调用。

长时间处于stopped形态，系统可能回收activity
onResume --> onSaveInstanceState() --> onDestory
重新创建
onCreate --> onRestoreIntanceState() --> onResume 
```java
protected void onSaveInstanceState(Bundle outState) {
    super.onSaveInstanceState(outState);
    savedInstanceState.putLong("param", value);
}
public void onCreate(Bundle savedInstanceState) {
    if (savedInstanceState != null){
        value = savedInstanceState.getLong("param");
    }
}
```

## 其他场景
### 转向下一个activity 
onPause --> onSaveInstanceState --> onStop

### 从下一个activity返回当前（按机器返回键）
onRestart --> onStart --> onResume --> onPostResume

### 转向上一个activity（按机器返回键）
onPause --> onStop --> onDestory

### 屏幕待机（黑掉）
onPause

### 屏幕变亮 
onResume --> onPostResume

### Home键退出 
onPause --> onSaveInstanceState --> onStop

### Home键导航返回 
onRestart --> onStart --> onResume --> onPostResume

### 旋转屏幕 
onPause --> onStop --> onDestory --> onCreate --> onStart --> onPostCreate --> onResume --> onPostResume
默认屏幕旋转会重新创建，当然可以通过在配置文件里加上如下代码：
```js
android:configChanges="keyboardHidden|orientation|screenSize"（sdk>13时需加上screenSize）
```
这个时候再旋转屏幕便不会销毁Activity，这时候再旋转屏幕可以看到只会执行onConfigurationChanged方法，有什么在屏幕旋转的逻辑可以重写这个方法：
```java
public void onConfigurationChanged(Configuration newConfig) {
    if (newConfig.orientation == ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE) {
        // TODO:
    }
    super.onConfigurationChanged(newConfig);
}
```

# Activity启动模式-AndroidMainifest启动模式

AndroidMainifest有四种启动模式，分别为standard，singleTop，singleTask，singleInstance。如果要使用这四种启动模式，必须在manifest文件中<activity>标签中的launchMode属性中配置，如：
```js
  <activity
            android:name=".StartModeActivity"
            android:launchMode="standard" />
```

## standard
标准启动模式，也是activity的默认启动模式。在这种模式下启动的activity可以被多次实例化，即在同一个任务中可以存在多个activity的实例，每个实例都会处理一个Intent对象。如果Activity A的启动模式为standard，并且A已经启动，在A中再次启动Activity A，即调用startActivity（new Intent（this，A.class）），会在A的上面再次启动一个A的实例，即当前的桟中的状态为A-->A。

## singleTop
如果一个以singleTop模式启动的activity的实例已经存在于任务桟的桟顶，那么再启动这个Activity时，不会创建新的实例，而是重用位于栈顶的那个实例，并且会调用该实例的onNewIntent()方法将Intent对象传递到这个实例中。举例来说，如果A的启动模式为singleTop，并且A的一个实例已经存在于栈顶中，那么再调用startActivity（new Intent（this，A.class））启动A时，不会再次创建A的实例，而是重用原来的实例，并且调用原来实例的onNewIntent()方法。这是任务桟中还是有一个A的实例。
如果以singleTop模式启动的activity的一个实例已经存在与任务桟中，但是不在桟顶，那么它的行为和standard模式相同，也会创建多个实例。

## singleTask
singleTask模式的Activity只允许在系统中有一个实例。如果系统中已经有了一个实例，持有这个实例的任务将移动到顶部，同时intent将被通过onNewIntent()发送。如果没有，则会创建一个新的Activity并置放在合适的任务中。

## singleInstance
总是在新的任务中开启，并且这个新的任务中有且只有这一个实例，也就是说被该实例启动的其他activity会自动运行于另一个任务中。当再次启动该activity的实例时，会重用已存在的任务和实例。并且会调用这个实例的onNewIntent()方法，将Intent实例传递到该实例中。和singleTask相同，同一时刻在系统中只会存在一个这样的Activity实例。比如MainActivity是singleInstance模式，应用A的任务栈创建了MainActivity，应用B也激活了MainActivity，则不需要再创建，两个应用共享MainActivity实例。

# Activity启动模式-Intent Flag启动模式
```java
Intent intent = new Intent(LifeCycleActivity.this, LifeCycleActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
startActivity(intent);
```
常用的Flag
## Intent.FLAG_ACTIVITY_NEW_TASK
使用一个新的Task来启动一个Activity，启动的每个Activity都将在一个新的Task中。这种通常使用在Service中启动Activity的场景，由于Service中并不存在Activity栈，使用该Flag来创建一个新的Activity栈，并创建新的Activity实例。

## Intent.FLAG_ACTIVITY_SINGLE_TOP
与 android:launchMode="singleTop"效果相同

## Intent.FLAG_ACTIVITY_CLEAR_TOP
与 android:launchMode="singleTask"效果相同

## Intent.FLAG_ACTIVITY_NO_HISTORY
当该Activity启动其他Activity时，该Activity就消失了，不保留Activity栈中。比如A启动B，B启动C，C又启动D，则当前Activity栈为ABD。