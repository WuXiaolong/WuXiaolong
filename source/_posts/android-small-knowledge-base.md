title: Android小知识库
date: 2015-08-10 14:32:49
tags: Android
category: [Android]
---
>这份是我工作以来，总结的小知识库，有些知识点现在看来太LOW了，把还觉得有用的分享出来！


<!--more-->

## Fragment设置隐藏或显示某个Fragment
MainFragment点击
```java
public void onItemClick(AdapterView<?> adapterView, View view,
                            int position, long id) {
       
        ((MainActivity) getActivity()).showImageFragment(true, mData.get(position).get("title").toString(), mData.get(position).get("imgUrl").toString());
        
    }
```
MainActivity
```java
public void showImageFragment(boolean show, String imgTxt, String imgUrl) {
        // showActionbarWithTabs(!show);
        if (show) {
            getSupportFragmentManager().beginTransaction()
                    .show(imageDetailFragment).commit();
            imageDetailFragment.setImgData(imgTxt, imgUrl);
        } else {
            getSupportFragmentManager().beginTransaction()
                    .hide(imageDetailFragment).commit();
        }

    }
```

## 获取arrt的值
不同主题下需要把颜色，数值写成attr属性
xml里，我们可以简单的引用attr属性值
```js
android:background="?attr/colorPrimary"
```

代码获取
```java
TypedValue typedValue = new TypedValue();
mContext.getTheme().resolveAttribute(R.attr.colorPrimary, typedValue, true);
int colorPrimary = typedValue.data;//value.data里面存储着的就是获取到的colorPrimary的值
```

## 拨号盘拨打电话
```java
Intent intent = new Intent(Intent.ACTION_CALL,
Uri.parse("tel:" + "400-036-1977"));
// intent.setAction(Intent.ACTION_CALL);// 直接拨号
intent.setAction(Intent.ACTION_DIAL);// 拨号盘
startActivity(intent);
```

## Drawable /Bitmap、String/InputStream、Bitmap/byte[]互转
[http://wuxiaolong.me/2015/08/10/Drawable-to-Bitmap/](http://wuxiaolong.me/2015/08/10/Drawable-to-Bitmap/)

## ProgressDialog
```java
final ProgressDialog progress = new ProgressDialog(LoginActivity.this);
		progress.setMessage("请稍等...");
		progress.setCanceledOnTouchOutside(false);
		progress.show();
progress.dismiss();
```

## 毫秒
毫秒Calendar.getInstance().getTimeInMillis()和System.currentTimeMillis()

## Fragment  setUserVisibleHint(boolean isVisibleToUser)
```java
 @Override
    public void setUserVisibleHint(boolean isVisibleToUser) {
        super.setUserVisibleHint(isVisibleToUser);
        if (isVisibleToUser) {
            //相当于Fragment的onResume
        } else {
            //相当于Fragment的onPause
        }
    }
```

## Fragment  onActivityResult
```java
public void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		getActivity();
		if (resultCode == Activity.RESULT_OK
				&& requestCode == AppUtils.REQUEST_CODE_ISLOGIN) {
			// 检查是否完善资料

			if (AppUtils.getSharedPreferences(getActivity()).getBoolean(
					"hasPersonalData", false)) {
				// 取本地存取是否完善资料,完善直接提示咨询对话框
				consultationDialog();
			} else {
				getCheckPersonalData();
			}

		}
	} 

startActivityForResult(intent,
							AppConfig.REQUEST_CODE_DIALOGUE);// 不是调用 getActivity().startActivityForResult()。
```
							
## dimen代码取值
getDimension方法获取到资源文件中定义的dimension值。
```js
Resources res = getResources();
float fontSize = res.getDimension(R.dimen.font_size);
```


## 数组初始化赋值
1、创建数组后，通过循环对数组赋值。
例如代码：
int [] nums = new int [100];
for(int i=0;i<10;i++){
nums[i] = i;
}
2、例如代码：
int [] nums = {0,1,2,3,4,5,6,7,8,9};
3、int [] nums = new int[]{0,1,2,3,4,5,6,7,8,9};

## Fragment.isAdded()
```java
public void switchFragment(Fragment newFragment, Fragment oldFragment) {
FragmentTransaction fragmentTransaction = getSupportFragmentManager()
.beginTransaction();
mCurrentFragment = newFragment;
if (newFragment.isAdded()) {
Log.i("wxl", oldFragment + "isAdded");
fragmentTransaction.hide(oldFragment).show(newFragment).commit();
} else {
Log.i("wxl", newFragment + "not isAdded");
fragmentTransaction.hide(oldFragment)
.add(R.id.container, newFragment).commit();

}
}
```
调用：
```java
switchFragment(HomeFragment.newInstance(), mCurrentFragment); 
public static HomeFragment homeFragment = null;

public static HomeFragment newInstance() {
if (homeFragment == null) {
homeFragment = new HomeFragment();
}
return homeFragment;
} 
```

## android之inputType属性
```js
<EditText android:layout_width="fill_parent" 
android:layout_height="wrap_content" 
android:inputType="phone" />
//文本类型，多为大写、小写和数字符号。
    android:inputType="none"
    android:inputType="text"
    android:inputType="textCapCharacters" 字母大写
    android:inputType="textCapWords" 首字母大写
    android:inputType="textCapSentences" 仅第一个字母大写
    android:inputType="textAutoCorrect" 自动完成
    android:inputType="textAutoComplete" 自动完成
    android:inputType="textMultiLine" 多行输入
    android:inputType="textImeMultiLine" 输入法多行（如果支持）
    android:inputType="textNoSuggestions" 不提示
    android:inputType="textUri" 网址
    android:inputType="textEmailAddress" 电子邮件地址
    android:inputType="textEmailSubject" 邮件主题
    android:inputType="textShortMessage" 短讯
    android:inputType="textLongMessage" 长信息
    android:inputType="textPersonName" 人名
    android:inputType="textPostalAddress" 地址
    android:inputType="textPassword" 密码
    android:inputType="textVisiblePassword" 可见密码
    android:inputType="textWebEditText" 作为网页表单的文本
    android:inputType="textFilter" 文本筛选过滤
    android:inputType="textPhonetic" 拼音输入
//数值类型
    android:inputType="number" 数字
    android:inputType="numberSigned" 带符号数字格式
    android:inputType="numberDecimal" 带小数点的浮点格式
    android:inputType="phone" 拨号键盘
    android:inputType="datetime" 时间日期
    android:inputType="date" 日期键盘
android:inputType="time" 时间键盘
```

## ImageView.ScaleType
（1）ImageView.ScaleType.center:图片位于视图中间，但不执行缩放。
（2）ImageView.ScaleType.CENTER_CROP 按统一比例缩放图片（保持图片的尺寸比例）便于图片的两维（宽度和高度）等于或者大于相应的视图的维度
（3）ImageView.ScaleType.CENTER_INSIDE按统一比例缩放图片（保持图片的尺寸比例）便于图片的两维（宽度和高度）等于或者小于相应的视图的维度
（4）ImageView.ScaleType.FIT_CENTER缩放图片使用center
（5）ImageView.ScaleType.FIT_END缩放图片使用END
（6）ImageView.ScaleType.FIT_START缩放图片使用START
（7）ImageView.ScaleType.FIT_XY缩放图片使用XY
（8）ImageView.ScaleType.MATRIX当绘制时使用图片矩阵缩放


## 调用系统发送短信界面
```java
/**
* 发送短信
* @param smsBody
*/
private void sendSMS(String smsBody)
{
//Uri smsToUri = Uri.parse("smsto:10000"); //如果想指定发送人
Uri smsToUri = Uri.parse("smsto:");
Intent intent = new Intent(Intent.ACTION_SENDTO, smsToUri);
intent.putExtra("sms_body", smsBody);
startActivity(intent);
}
```

## 跳转市场搜索某款软件
```java
Intent intent = new Intent(
				"android.intent.action.VIEW");
		intent.setData(Uri
				.parse("market://details?id=com.adobe.flashplayer"));
		startActivity(intent);
```

## 检测系统中是否安装某款软件
```java
//检测系统中是否已经安装了adobe flash player插件，插件的packageName是com.adobe.flashplayer：
private boolean check() {
		PackageManager pm = getPackageManager();
		List<PackageInfo> infoList = pm
				.getInstalledPackages(PackageManager.GET_SERVICES);
		for (PackageInfo info : infoList) {
			if ("com.adobe.flashplayer".equals(info.packageName)) {
				return true;
			}
		}
		return false;
}

private void isAvilible(String packageName) {

		PackageInfo packageInfo;
		try {
			packageInfo = this.getPackageManager().getPackageInfo(packageName,
					0);

		} catch (NameNotFoundException e) {
			packageInfo = null;
			e.printStackTrace();
		}
		if (packageInfo != null) {
//1、通过包名
			Intent intent = new Intent();
			intent = getPackageManager().getLaunchIntentForPackage(packageName);
			startActivity(intent);
//2、通过类名： 
Intent intent=new Intent();  
intent.setComponent(new ComponentName(packageName, "com.joe.internet.Main"));  
startActivity(intent);  
		} 

	}
```

## 对话框菜单
```java
new AlertDialog.Builder(this)
						.setTitle("choice")
						.setItems(new String[] { "选择1", "选择2", "选择3", "选择4" },
								new DialogInterface.OnClickListener() {

									@Override
									public void onClick(DialogInterface dialog,
											int which) {
										Toast.makeText(MyContentActivity.this,
												which + "", Toast.LENGTH_SHORT)
												.show();

									}
								}).show();
```

## 定义ProgressBar 
```js
<ProgressBar
        android:id="@+id/mProgress"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"          android:indeterminateDrawable="@drawable/progress_rotate" />
```
progress_rotate：
```js
<?xml version="1.0" encoding="UTF-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item>
        <rotate
            android:drawable="@drawable/progressbar"
            android:duration="300"
            android:fromDegrees="0.0"
            android:pivotX="50.0%"
            android:pivotY="50.0%"
            android:toDegrees="360.0" />
    </item>
</layer-list>
```

## 幻灯片效果
xml
```js
<ProgressBar
        android:id="@+id/ProgressBar01"
        style="@style/animStyle"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_centerInParent="true" />
```
style
```js
<style name="animStyle" parent="@android:style/Widget.ProgressBar.Large">
        <item name="android:indeterminateDrawable">@anim/test</item>
</style>
```
anim
```js
<?xml version="1.0" encoding="UTF-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false" >

    <item
        android:drawable="@drawable/t1"
        android:duration="500"/>
    <item
        android:drawable="@drawable/t2"
        android:duration="500"/>
    <item
        android:drawable="@drawable/t3"
        android:duration="500"/>
    <item
        android:drawable="@drawable/t4"
        android:duration="500"/>
    <item
        android:drawable="@drawable/t5"
        android:duration="500"/>

</animation-list>
```

## MD5加密
```java
public String Md5(String plainText) {
		String result = "";
		try {
			MessageDigest md = MessageDigest.getInstance("MD5");
			md.update(plainText.getBytes());
			byte b[] = md.digest();

			int i;

			StringBuffer buf = new StringBuffer("");
			for (int offset = 0; offset < b.length; offset++) {
				i = b[offset];
				if (i < 0)
					i += 256;
				if (i < 16)
					buf.append("0");
				buf.append(Integer.toHexString(i));
			}
			result = buf.toString().toUpperCase();// 32位的加密（转成大写）

			buf.toString().substring(8, 24);// 16位的加密

		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
		}
		return result;
	}
```

## 设置自动跳转页面
### Timer
```java
Timer timer = new Timer();
		timer.schedule(new TimerTask() {
			@Override
			public void run() {
				// TODO Auto-generated method stub
				Intent goIntent = new Intent();
				goIntent.setClass(LauncherActivity.this, DemoActivity.class);
				startActivity(goIntent);
			}
		}, 3 * 1000);
	}
```

### Handler
```java
new Handler().postDelayed(new Runnable() {
			@Override
			public void run() {
				startActivity(new Intent(SplashScreen.this,
						DomobSampleActivity.class));
				finish();
			}
		}, 1000);
```

## 随机取数
```java
List<Integer> list = new ArrayList<Integer>();
		for (int i = 0; i < 10; i++) {
			list.add(i);
		}
		Collections.shuffle(list);
		for (int v : list) {
			Log.d("wxl", "V===" + v);
		}
```
或：
```java
Random random = new Random();
int ran = random.nextInt(keywordsList.size());
			String tmp = keywordsList.get(ran).get("keyword").toString();
```

## selector下的属性值

android:state_pressed
如果是true，当被点击时显示该图片，如果是false没被按下时显示默认。
android:state_focused
true，获得焦点时显示；false，没获得焦点显示默认。
android:state_selected
true，当被选择时显示该图片；false，当未被选择时显示该图片。
android:state_checkable
true，当	能使用时显示该图片；false，当CheckBox不能使用时显示该图片。
android:state_checked
true，当CheckBox选中时显示该图片；false，当CheckBox为选中时显示该图片。
android:state_enabled
true，当该组件能使用时显示该图片；false，当该组件不能使用时显示该图片。
android:state_window_focused
true，当此activity获得焦点在最前面时显示该图片；false，当没在最前面时显示该图片。
```js
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true"
          android:drawable="@drawable/button_pressed"/><!-- pressed -->
    <item android:state_focused="true"
          android:drawable="@drawable/button_focused"/><!-- focused -->
    <item android:drawable="@drawable/button_normal"/><!-- default -->
</selector>
```

## 带下划线的EditText
```java
public class LinedEditText extends EditText {
	private Paint linePaint;
	private float margin;
	private int paperColor;

	public LinedEditText(Context paramContext, AttributeSet paramAttributeSet) {
		super(paramContext, paramAttributeSet);
		this.linePaint = new Paint();
	}
	@Override
	protected void onDraw(Canvas paramCanvas) {
		paramCanvas.drawColor(this.paperColor);
		int i = getLineCount();// 得到总的行數
		int j = getHeight();// 获得TextView的高度
		int k = getLineHeight();// 获得TextView的行高
		int m = j / k + 1;// 总的线数
		if (i < m)
			i = m;
		int n = getCompoundPaddingTop();
		Log.d("wxl", "n----" + n);
		paramCanvas.drawLine(0.0F, n, getRight(), n, this.linePaint);
		for (int i2 = 0;; i2++) {
			if (i2 >= i) {
				setPadding(10 + (int) this.margin, 0, 0, 0);
				super.onDraw(paramCanvas);
				paramCanvas.restore();
				return;
			}
			n += k;
			paramCanvas.drawLine(0.0F, n, getRight(), n, this.linePaint);
			paramCanvas.save();
		}
	}
}
```
主要工作就是重载onDraw方法，利用从TextView继承下来的getLineCount函数获取文本所占的行数，以及getLineBounds来获取特定行的基准高度值，而且这个函数第二个参数会返回此行的“外包装”值。再利用这些值绘制这一行的线条。为了让界面的View使用自定义的EditText类，必须在配置文件中进行设置



## 关闭键盘
```java
public static void hideSoftInput(Activity activity) {
        if (activity.getCurrentFocus() != null)
            ((InputMethodManager) activity
                    .getSystemService(Context.INPUT_METHOD_SERVICE))
                    .hideSoftInputFromWindow(activity.getCurrentFocus()
                            .getWindowToken(), InputMethodManager.HIDE_NOT_ALWAYS);
    }
```

## 界面不被弹出的键盘盖住
一进入activity就弹出键盘
```js
android:windowSoftInputMode="adjustResize" 
android:windowSoftInputMode="adjustPan|stateAlwaysVisible"
```
第一个软键盘和输入框会完全分离，而第二个输入框还是会被软键盘盖住一点点
而且第二个要把两个属性叠加起来，为什么要叠加起来呢，因为如果只用adjustPan 的话需要点击输入框才会弹出软键盘

在 AndroidMainfest.xml中选择哪个activity，设置windowSoftInputMode属性为 
```js
adjustUnspecified|stateHidden
android:windowSoftInputMode="adjustUnspecified|stateHidden"
```

## 控件美化Shape
```js
<shape>
 <!-- 实心 -->
            <solid android:color="#ff9d77"/>
            <gradient//渐变
               android:startColor="#ff8c00"
               android:endColor="#FFFFFF"
               android:angle="270" />
            <stroke//边缘 描边
               android:width="2dp"
               android:color="#dcdcdc" />
            <corners//拐角
               android:radius="2dp" />
            <padding
               android:left="10dp"
               android:top="10dp"
               android:right="10dp"
               android:bottom="10dp" />
        </shape>
```
对于上面， shape的定义
solid：实心，就是填充的意思，
android:color指定填充的颜色

渐变，在gradient中startColor属性为开始的颜色，
endColor为渐变结束的颜色，
angle是渐变角度，必须为45的整数倍。
另外渐变默认的模式为android:type="linear"，即线性渐变，可以指定渐变为径向渐变，android:type="radial"，径向渐变需要指定半径android:gradientRadius="50"。；

stroke可以理解为边缘android:width="2dp" 描边的宽度，android:color 描边的颜色。我们还可以把描边弄成虚线的形式，设置方式为：android:dashWidth="5dp" 
android:dashGap="3dp"
其中android:dashWidth表示'-'这样一个横线的宽度，android:dashGap表示之间隔开的距离。；

corners为拐角这里radius属性为半径，android:radius为角的弧度，值越大角越圆。
我们还可以把四个角设定成不同的角度，方法为：
```js
<corners 
        android:topRightRadius="20dp"    右上角
        android:bottomLeftRadius="20dp"    右下角
        android:topLeftRadius="1dp"    左上角
        android:bottomRightRadius="0dp"    左下角
 />
```
这里有个地方需要注意，bottomLeftRadius是右下角，而不是左下角，这个有点郁闷，不过不影响使用，记得别搞错了就行。
最后是相对位置属性padding。
对于一个Button完整的定义可以为（drawable下）
```js
  <?xmlversion="1.0" encoding="utf-8"?>
<selector
    xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" >
        <shape>
            <gradient
               android:startColor="#ff8c00"
               android:endColor="#FFFFFF"
               android:angle="270" />
            <stroke
               android:width="2dp"
               android:color="#dcdcdc" />
            <corners
               android:radius="2dp" />
            <padding
               android:left="10dp"
               android:top="10dp"
               android:right="10dp"
               android:bottom="10dp" />
        </shape>
    </item>
   <item android:state_focused="true" >
        <shape>
            <gradient
               android:startColor="#ffc2b7"
               android:endColor="#ffc2b7"
               android:angle="270" />
            <stroke
               android:width="2dp"
               android:color="#dcdcdc" />
            <corners
               android:radius="2dp" />
            <padding
               android:left="10dp"
               android:top="10dp"
               android:right="10dp"
               android:bottom="10dp" />
        </shape>
    </item>
    <item>       
        <shape>
            <gradient
               android:startColor="#ff9d77"
               android:endColor="#ff9d77"
               android:angle="270" />
            <stroke
               android:width="2dp"
               android:color="#fad3cf" />
            <corners
               android:radius="2dp" />
            <padding
               android:left="10dp"
               android:top="10dp"
               android:right="10dp"
               android:bottom="10dp" />
        </shape>
    </item>
</selector>
```
以上几个item的区别主要是体现在state_pressed按下或state_focused获得焦点时，当当来判断显示什么类型，而没有state_xxx属性的item可以看作是常规状态下。

## 获取版本名称 VersionName
```java
public String getVersionName(Context context) {
		PackageManager manager = context.getPackageManager();
		String packageName = context.getPackageName();
		try {
			PackageInfo info = manager.getPackageInfo(packageName, 0);
			return info.versionName;
		} catch (NameNotFoundException e) {
			return "1.0";
		}
	}
```

## 获取Android手机设备的IMSI / IMEI 信息
```java
TelephonyManager mTelephonyMgr = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
String imsi = mTelephonyMgr.getSubscriberId(); 
String imei = mTelephonyMgr.getDeviceId(); 
Log.i("wxl", "imsi="+imsi);为null
Log.i("wxl", "imei="+imei); 
另外不要忘了在AndroidManifest.xml中加上读取手机状态的权限
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
```

## 自定义Log是否显示
```java
public class Log {
    private static final boolean LOG = true;
    public static void i(String tag, String msg) {
        if (LOGV)
            android.util.Log.i(tag , msg);
    }
    public static void d(String tag, String msg) {
        if (LOGV)
            android.util.Log.d(tag , msg);
    }
    public static void w(String tag, String msg) {
        if (LOGV)
            android.util.Log.w(tag , msg);
    }
    public static void w(String tag, String msg, Throwable tr) {
        if (LOGV)
            android.util.Log.w(tag , msg, tr);
    }
    public static void v(String tag, String msg) {
        if (LOGV)
            android.util.Log.v(tag , msg);
    }
    public static void e(String tag, String msg) {
        android.util.Log.e(tag , msg);
    }
    public static void e(String tag, String msg, Throwable tr) {
        android.util.Log.e( tag , msg, tr);
    }
}
```

## 异步操作AsyncTask 
```java
参数1：向后台任务的执行方法传递参数的类型 ； 
参数2：在后台任务执行过程中，要求主UI线程处理中间状态，通常是一些UI处理中传递的参数类型；
参数3：后台任务执行完返回时的参数类型。
 private class MyAsyncTask extends AsyncTask<Integer, String, Void> {

		// 实现抽象方法doInBackground()，代码将在后台线程中执行，由execute()触发
		protected Void doInBackground(Integer... params) {
			return null;
		}

		// 任务启动，可以在这里显示一个对话框，这里简单处理
		protected void onPreExecute() {
			super.onPreExecute();
		}

		// 取消
		protected void onCancelled() {
			super.onCancelled();
		}

		// 定义后台进程执行完后的处理
		protected void onPostExecute(Void result) {
			super.onPostExecute(result);
			Toast.makeText(MainActivity.this, "OK", Toast.LENGTH_SHORT).show();
		}

		// 更新进度,在UI主线程执行的内容，将item加入list中。方法中的参数为范式方式，实质为数组，由于我们只传递了item一个String，要获取，为values[0]
		protected void onProgressUpdate(String... values) {
			super.onProgressUpdate(values);
		}

	}
```
调用：
```java
new MyAsyncTask().execute(参数1);// 创建后台任务的对象
```

## 检查网络状态
```java
public boolean checkNetworkInfo() {
		ConnectivityManager conMan = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
		// mobile 3G Data Network
		State mobile = conMan.getNetworkInfo(ConnectivityManager.TYPE_MOBILE)
				.getState();
		// wifi
		State wifi = conMan.getNetworkInfo(ConnectivityManager.TYPE_WIFI)
				.getState();
		// 如果3G网络和wifi网络都未连接，且不是处于正在连接状态 则进入Network Setting界面 由用户配置网络连接
		if (mobile == State.CONNECTED || mobile == State.CONNECTING)
			return true;
		if (wifi == State.CONNECTED || wifi == State.CONNECTING)
			return true;
		AlertDialog.Builder builder = new AlertDialog.Builder(this);
		builder.setMessage(getResources().getString(R.string.no_network))
				.setCancelable(false)
				.setPositiveButton(
						getResources().getString(R.string.configuration),
						new DialogInterface.OnClickListener() {
							public void onClick(DialogInterface dialog, int id) {

								// 进入无线网络配置界面
								startActivity(new Intent(
										Settings.ACTION_WIRELESS_SETTINGS));
								MainActivity.this.finish();
							}
						})
				.setNegativeButton(getResources().getString(R.string.quit),
						new DialogInterface.OnClickListener() {
							public void onClick(DialogInterface dialog, int id) {
								MainActivity.this.finish();
							}
						});
		builder.show();
		return false;
	}
```
代码判断调用：
```java
// 检查网络状态
		if (!checkNetworkInfo()) {
			return;
		}
```

## Html.fromHtml（）
让数据接受网页的格式
```java
URLEncoder.encode(String s); 网址请求中文解析
&#8230;代表省略号
```
## URLEncoder.encode
网址请求带中文
```java
URLEncoder.encode(content, "utf-8")
```

## Android中字体加粗
* 在xml文件中使用
```js
android:textStyle=”bold”
```
* Java 代码

（网上查XML不能将中文设置成粗体，只能通过Java code。Android 2.2 可以通过XML将中文设置成粗体）

```java
  TextView tv = (TextView)findViewById(R.id.TextView01); 
  TextPaint tp = tv.getPaint();
  tp.setFakeBoldText(true);
```
* Html.fromHtml("\<\b>")

```java
(1)viewHolder.title.setText(Html.fromHtml("<b>"
				+ listTitle.get(position).get("title") + "</b>"));
(2)TextView.setText(Html.fromHtml("<font color=#FF0000>hello</font>"));
```

## 代码设置背景图setImageResource/setBackgroundResource
```java
textView.setBackgroundResource(R.drawable.bg_menu_1);设置背景图片
textView.setBackgroundColor(0xffffffff);
setImageResource與xml中的src的屬性才是相匹配的，而setBackgroundResource是與xml中的background屬性相匹配
的
holder.chat_sound_iv_right.setBackgroundResource(0);//没有图片
```

## android中的ellipsize
```java
用法如下：
在xml中
android:ellipsize = "end"　　  省略号在结尾
android:ellipsize = "start" 　　省略号在开头
android:ellipsize = "middle"     省略号在中间
android:ellipsize = "marquee"  跑马灯
最好加一个约束android:singleline = "true"
跑马灯
android:singleLine="true"
android:ellipsize="marquee"
android:focusableInTouchMode="true"
android:focusable="true"
android:marqueeRepeatLimit="marquee_forever"
android:singleLine=true 表示使用单行文字，多行文字也就无所谓使用Marquee效果了。
android:marqueeRepeatLimit，设置走马灯滚动的次数。
android:ellipsize，设置了文字过长时如何切断文字，可以有none, start,middle, end, 如果使用走马灯效果则设为marquee.
android:focusable，Android的缺省行为是在控件获得Focus时才会显示走马灯效果
```

## 控件点击效果
```js
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_focused="true" android:state_pressed="false" android:drawable="@drawable/professional_1"/>
    <item android:state_focused="false" android:state_pressed="true" android:drawable="@drawable/professional_1"/>
    <item android:drawable="@drawable/professional"/>
</selector>
```

## 保留小数点
方法一：
```java
private DecimalFormat df;
df = new DecimalFormat("0.0");
float data;
df.format(data);//返回值
```
方法二：
```java
double juli = 1569;
String result = String.format("%.1f", juli/1000);
```

## RadioGroup
```js
<RadioGroup
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:orientation="vertical" >

       <RadioButton
           android:id="@+id/open"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_marginLeft="10dip"
           android:layout_marginTop="10dip"
           android:button="@drawable/radiobutton"
           android:checked="true"
           android:text="@string/open_album"
           android:textColor="#000000"
           android:textSize="16dip" />

       <RadioButton
           android:id="@+id/secrecy"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_marginLeft="10dip"
           android:button="@drawable/radiobutton"
           android:text="@string/secrecy_album"
           android:textColor="#000000"
           android:textSize="16dip" />
   </RadioGroup>

```
android:button="@null"  去除RadioButton前面的圆点
android:background="@drawable/radio" 使用定义的样式

## 占位符%s替换
%d   （表示整数）
%f    （表示浮点数）
%s   （表示字符串）
txt.setText(String.format (“被替换%1$s”,”替换内容”));

## TextView中嵌套图片
```java
Drawable draw = getResources().getDrawable(R.drawable.ji_dot_nor);
textView.setCompoundDrawablesWithIntrinsicBounds(null, draw, null, null);  

 setCompoundDrawablesWithIntrinsicBounds(left, top, right, bottom)
   意思是设置Drawable显示在text的左、上、右、下位置。
  （Textview、Button都可以）
```

## TextView做成分割线
```js
<TextView
     android:id="@id/textView2"
     android:layout_width="fill_parent"
     android:layout_height="2.0px"
android:background="@color/orange" />
```

## SpannableString单击文字链接
```java
SpannableString spannableString = new SpannableString(str);
		spannableString.setSpan(new ClickableSpan() {
			public void onClick(View view) {
				// Intent intent = new Intent(ApkTest.this, Apk.class);
				Intent intent = new Intent(Intent.ACTION_CALL, Uri.parse("tel:"
						+ "15261589767"));
				startActivity(intent);
			}
		}, 0, str.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
		TextView.setText(spannableString);
		TextView.setMovementMethod(LinkMovementMethod.getInstance());
```

## TextView中文字通过SpannableString
来设置超链接、颜色、字体等属性
```java
String  title =”123456”;
SpannableString titleStr = new SpannableString(title);
titleStr.setSpan(new ForegroundColorSpan(Color.RED), 0,
						titleStr.length(), 0);
textView.setText(titleStr);
```

## android:drawableTop属性
写在TextView里，实现图片+文字

## 获取分辨率
```java
DisplayMetrics displayMetrics = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(displayMetrics);

displayMetrics.widthPixels + "x"
				+ displayMetrics.heightPixels;
```
## 代码完成控件

Button
```java
linearLayout = (LinearLayout) findViewById(R.id.linearLayout2);
LayoutParams layoutParams = new LinearLayout.LayoutParams(0,
				LinearLayout.LayoutParams.WRAP_CONTENT, 1);//1均分属性
layoutParams.setMargins(10, 10, 10, 10);

Button button = new Button(MainActivity.this);
button.setLayoutParams(layoutParams);
linearLayout.addView(button);
```
TextView
```java
private TextView overlay;
private void initOverlay() {
		LayoutInflater inflater = LayoutInflater.from(this);
		overlay = (TextView) inflater.inflate(R.layout.overlay, null);
		overlay.setVisibility(View.INVISIBLE);
		WindowManager.LayoutParams lp = new WindowManager.LayoutParams(
				LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT,
				WindowManager.LayoutParams.TYPE_APPLICATION,
				WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE
						| WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE,
				PixelFormat.TRANSLUCENT);
		WindowManager windowManager = (WindowManager) this
				.getSystemService(Context.WINDOW_SERVICE);
		windowManager.addView(overlay, lp);
	}
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:background="#ffffff"
    android:gravity="center"
    android:maxWidth="80dip"
    android:minWidth="80dip"
    android:padding="5dip"
    android:textColor="#3399ff"
    android:textSize="70sp" />


private LinearLayout.LayoutParams layoutParams = null;
		private LinearLayout.LayoutParams btnParmas;

layoutParams = new LinearLayout.LayoutParams(
							LinearLayout.LayoutParams.WRAP_CONTENT,
							LinearLayout.LayoutParams.WRAP_CONTENT);
					layoutParams.setMargins(
							(int) getResources().getDimension(
									R.dimen.lable_left), 0, 0, 0);
					btnParmas = new LinearLayout.LayoutParams(
							LinearLayout.LayoutParams.WRAP_CONTENT,
							(int) getResources().getDimension(
									R.dimen.lable_height));

					for (int i = 0; i < lables.length; i++) {
						LinearLayout linearLayout = new LinearLayout(activity);
						lableColor = random.nextInt(lableColors.length);
						Button lable = new Button(activity);
						lable.setText(lables[i]);
						lable.setGravity(Gravity.CENTER_HORIZONTAL
								| Gravity.CENTER_VERTICAL);
						lable.setTextSize(getResources().getDimension(
								R.dimen.lable));
						lable.setTextColor(getResources().getColor(
								R.color.white));
						lable.setBackgroundResource(lableColors[i]);
						linearLayout.addView(lable, btnParmas);
						holder.play_lables.addView(linearLayout, layoutParams);
					}
```
## TextWatcher
```java
editText1.addTextChangedListener(watcher);
TextWatcher watcher = new TextWatcher() {
		public void onTextChanged(CharSequence arg0, int arg1, int arg2,
				int arg3) {
		}
		public void beforeTextChanged(CharSequence arg0, int arg1, int arg2,
				int arg3) {
		}
		public void afterTextChanged(Editable arg0) {			
		}
	};
```
## 屏幕旋转时不销毁
```js
AndroidManifest加入
android:configChanges="orientation|keyboardHidden"
```
获取屏幕方向
```java
Configuration newConfig = getResources().getConfiguration();    
        if (newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE){    
            //横屏    
         }else if(newConfig.orientation == Configuration.ORIENTATION_PORTRAIT){    
            //竖屏    
         }else if(newConfig.hardKeyboardHidden == Configuration.KEYBOARDHIDDEN_NO){    
            //键盘没关闭。屏幕方向为横屏    
         }else if(newConfig.hardKeyboardHidden == Configuration.KEYBOARDHIDDEN_YES){    
            //键盘关闭。屏幕方向为竖屏    
         } 
```

## List增加数据
### 简单
```java
private List<String> getData() {
		List<String> data = new ArrayList<String>();
		data.add("123");
		data.add("345");
		data.add("456");
		data.add("567");
		return data;
	}
```
### 键值对
```java
private List<Map<String, Object>> getData() {
        List<Map<String, Object>> list = new ArrayList<Map<String, Object>>();
 
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("title", "G1");
        map.put("info", "google 1");
        map.put("img", R.drawable.i1);
        list.add(map);
 
        map = new HashMap<String, Object>();
        map.put("title", "G2");
        map.put("info", "google 2");
        map.put("img", R.drawable.i2);
        list.add(map);
 
        map = new HashMap<String, Object>();
        map.put("title", "G3");
        map.put("info", "google 3");
        map.put("img", R.drawable.i3);
        list.add(map);
         
        return list;
    }
```
清除数据：
```java
data.clear();
listView.setAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_expandable_list_item_1,getData()));
```
### LinkedList
```java
LinkedList<String> list = new LinkedList<String>();
list.addFirst("000");
```

## 验证是否手机号码
```java
public boolean isMobileNO(String mobiles) {
		String expression = "((^(13|15|18)[0-9]{9}$)|(^0[1,2]{1}\\d{1}-?\\d{8}$)|(^0[3-9] {1}\\d{2}-?\\d{7,8}$)|(^0[1,2]{1}\\d{1}-?\\d{8}-(\\d{1,4})$)|(^0[3-9]{1}\\d{2}-? \\d{7,8}-(\\d{1,4})$))";
		Pattern pattern = Pattern.compile(expression);
		Matcher matcher = pattern.matcher(mobiles);
		Log.d("wxl", matcher.matches() + "");
		return matcher.matches();
	}
```

## 正则表达式数字验证
```java
public boolean isNumber(String str)
    {
        java.util.regex.Pattern pattern=java.util.regex.Pattern.compile("[0-9]*");
        java.util.regex.Matcher match=pattern.matcher(str);
        if(match.matches()==false)
        {
             return false;
        }
        else
        {
             return true;
        }
    }
```

## SharedPreferences
### SharedPreferences存数据
```java
SharedPreferences sharedPreferences = getSharedPreferences("test", Context.MODE_PRIVATE);
Editor editor = sharedPreferences.edit();//获取编辑器
editor.putString("name", "张三");
editor.putInt("age", 24);
editor.putBoolean("AutoLogin", false);

editor.commit();//提交修改
```
### SharedPreferences取数据
```java
SharedPreferences sharedPreferences = getSharedPreferences("test", Context.MODE_PRIVATE);
//getString()第二个参数为缺省值,如果preference中不存在该key,将返回缺省值
String name = sharedPreferences.getString("name", "");
int age = sharedPreferences.getInt("age", 1);
boolean autoLogin= sharedPreferences. getBoolean("AutoLogin", false);
```
## Intent 传值

### 简单传值
ActivityPage
```java
Intent intent = new Intent(ActivityPage.this, Player.class);
			//保存信息
			Bundle mBundle = new Bundle();
			mBundle.putString("realname ", realname);
			intent.putExtras(mBundle);
```
Player接受
```java
realname = this.getIntent().getStringExtra("realname");
Intent intent = new Intent();
intent.setClass(HomeActivity.this, CompanyActivity.class);
intent.putExtra("mCompanySupply", mCompanySupply);
				startActivity(intent);
CompanyActivity.class:
ArrayList<ClassifySub3Bean>  mCompanySupply = (ArrayList<ClassifySub3Bean>) getIntent()
				.getSerializableExtra("mCompanySupply");
```
## 复杂传值
A页面：
```java
private static final int REQUEST_CODE_A = 0;
private static final int RESULT_CODE_B = 0;
Intent intent = new Intent();
				Bundle bundle = new Bundle();
				intent.setClass(MainActivity.this, TestBActivity.class);
				bundle.putString("msg", textView.getText().toString());
				intent.putExtras(bundle);
				startActivityForResult(intent, REQUEST_CODE_A);

protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (requestCode == REQUEST_CODE_A && resultCode == RESULT_OK) {
			String str=data.getExtras().getString("result");
		}
	}
```
B页面
```java
private static final int RESULT_CODE_B = 0;
Intent intent = new Intent();
				intent.putExtra("result", textView.getText().toString());
				setResult(RESULT_OK, intent);
				finish();

public boolean onKeyDown(int keyCode, KeyEvent event) {
		if (keyCode == KeyEvent.KEYCODE_BACK) {
			Intent intent = new Intent();
			intent.putExtra("result", "直接返回");
			setResult(RESULT_OK, intent);
			this.finish();
			return true;
		} else {
			return super.onKeyDown(keyCode, event);
		}
	}
```
### List
```java
Intent intent = new Intent();
					intent.putStringArrayListExtra("mSelectedPhotos",
							mSelectedPhotos);
					setResult(RESULT_OK, intent);
					
					
					ArrayList<String> mSelectedPhotos = (ArrayList<String>) intent
						.getStringArrayListExtra("mSelectedPhotos");
```
## xml文件中导入另一个xml文件的方法include
```js
<include 
android:id="@+id/included1"
layout="@layout/anotherlayout" />
```
## 单击返回键两次退出
```java
private static Boolean isExit = false;
        @Override
        public boolean onKeyDown(int keyCode, KeyEvent event) {
                if (keyCode == KeyEvent.KEYCODE_BACK) {
                        if (isExit == false) {
                                isExit = true;
                                Toast.makeText(this, "再按一次退出程序", Toast.LENGTH_SHORT).show();
                                new Timer().schedule(new TimerTask() {
                                        @Override
                                        public void run() {
                                                isExit = false;
                                        }
                                }, 2000);
                        } else {
                                finish();
                                System.exit(0);
                        }
                }
                return false;
        }
```
或者
```java
@Override 
    public boolean onKeyUp(int keyCode, KeyEvent event) { 
        if (keyCode == KeyEvent.KEYCODE_BACK) { 
            long secondTime = System.currentTimeMillis(); 
            if (secondTime - firstTime > 800) {//如果两次按键时间间隔大于800毫秒，则不退出 
                Toast.makeText(MainActivity.this, "再按一次退出程序...", 
                        Toast.LENGTH_SHORT).show(); 
                firstTime = secondTime;//更新firstTime 
                return true; 
            } else { 
                System.exit(0);//否则退出程序 
            } 
        } 
        return super.onKeyUp(keyCode, event); 
    }
```

## 图片左右循环移动
```java
TranslateAnimation left, right;
right = new TranslateAnimation(Animation.RELATIVE_TO_PARENT, -1f,
				Animation.RELATIVE_TO_PARENT, -2f,
				Animation.RELATIVE_TO_PARENT, 0f, Animation.RELATIVE_TO_PARENT,
				0f);
		left = new TranslateAnimation(Animation.RELATIVE_TO_PARENT, -2f,
				Animation.RELATIVE_TO_PARENT, -1f,
				Animation.RELATIVE_TO_PARENT, 0f, Animation.RELATIVE_TO_PARENT,
				0f);
		right.setDuration(25000);
		left.setDuration(25000);
		right.setFillAfter(true);
		left.setFillAfter(true);

		right.setAnimationListener(new Animation.AnimationListener() {
			@Override
			public void onAnimationStart(Animation animation) {
			}

			@Override
			public void onAnimationRepeat(Animation animation) {
			}

			@Override
			public void onAnimationEnd(Animation animation) {
				runImage.startAnimation(left);
			}
		});
		left.setAnimationListener(new Animation.AnimationListener() {
			@Override
			public void onAnimationStart(Animation animation) {
			}

			@Override
			public void onAnimationRepeat(Animation animation) {
			}

			@Override
			public void onAnimationEnd(Animation animation) {
				runImage.startAnimation(right);
			}
		});
		runImage.startAnimation(right);
```


## 控件左右抖动
res下anim
animlayout.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<translate xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000"
    android:fromXDelta="0"
    android:interpolator="@anim/cycle_7"
    android:toXDelta="10" />
```
cycle_7.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<cycleInterpolator xmlns:android="http://schemas.android.com/apk/res/android"
android:cycles="7" />
```
(2)java代码
```java
Animation shake = AnimationUtils.loadAnimation(
						MainActivity.this, R.anim.animlayout);
				btn.setAnimation(shake);
```