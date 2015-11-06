title: Android之Intent和Intent过滤器
date: 2015-11-03 09:40:54
tags: Intent
category: Android
---
Intent 是一个消息传递对象，您可以使用它从其他应用组件请求操作。尽管 Intent 可以通过多种方式促进组件之间的通信，但其基本用例主要包括以下三个：
* 启动 Activity
 Activity 表示应用中的一个屏幕。通过将 Intent 传递给 startActivity()，您可以启动新的 Activity 实例。Intent 描述了要启动的 Activity，并携带了任何必要的数据。
如果您希望在 Activity 完成后收到结果，请调用 startActivityForResult()。在 Activity 的 onActivityResult() 回调中，您的 Activity 将结果作为单独的 Intent 对象接收。
* 启动服务
[Android之Service](http://wuxiaolong.github.io/2015/11/02/service/)
* 传递广播
[Android之BroadcastReceiver](http://wuxiaolong.github.io/2015/11/03/BroadcastReceiver/)
<!--more-->

## Intent 类型
Intent 分为两种类型：

* 显式 Intent 
按名称（完全限定类名）指定要启动的组件。通常，您会在自己的应用中使用显式 Intent 来启动组件，这是因为您知道要启动的 Activity 或服务的类名。例如，启动新 Activity 以响应用户操作，或者启动服务以在后台下载文件。
* 隐式 Intent
不会指定特定的组件，而是声明要执行的常规操作，从而允许其他应用中的组件来处理它。例如，如需在地图上向用户显示位置，则可以使用隐式 Intent，请求另一具有此功能的应用在地图上显示指定的位置。

## 显式 Intent 示例
要创建显式 Intent，请为 Intent 对象定义组件名称。Intent 的所有其他属性均为可选属性。
```java
Intent intent = new Intent(this, DownloadActivity.class);
startActivity(intent);
```

## 隐式 Intent 示例
隐式 Intent 指定能够在可以执行相应操作的设备上调用任何应用的操作。如果您的应用无法执行该操作而其他应用可以，且您希望用户选取要使用的应用，则使用隐式 Intent 非常有用。
例如，如果您希望用户与他人共享您的内容，请使用 ACTION_SEND 操作创建 Intent，并添加指定共享内容的 Extra。使用该 Intent 调用 startActivity() 时，用户可以选取共享内容所使用的应用。
```java
// Create the text message with a string
Intent sendIntent = new Intent();
sendIntent.setAction(Intent.ACTION_SEND);
sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
sendIntent.setType("text/plain");

// Verify that the intent will resolve to an activity
if (sendIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(sendIntent);
}
```
查看地图:
```java
// Map point based on address
Uri location = Uri.parse("geo:0,0?q=1600+Amphitheatre+Parkway,+Mountain+View,+California");
// Or map point based on latitude/longitude
// Uri location = Uri.parse("geo:37.422219,-122.08364?z=14"); // z param is zoom level
Intent mapIntent = new Intent(Intent.ACTION_VIEW, location);
```
查看网页:
```java
Uri webpage = Uri.parse("http://www.android.com");
Intent webIntent = new Intent(Intent.ACTION_VIEW, webpage);
```
如果多个 Activity 接受 Intent，则系统将显示一个对话框，使用户能够选取要使用的应用。

## Intent 传值示例

* 简单传值

A页面
```java
Intent intent = new Intent(A.this, B.class);
intent.putExtras("realname ", realname);
startActivity(intent );
```

B页面
```java
realname = this.getIntent().getStringExtra("realname");
```

* 传值接受返回结果

A页面
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
//返回设置
Intent intent = new Intent();
intent.putExtra("result", textView.getText().toString());
setResult(RESULT_OK, intent);
finish();
```

* 传List

第一种
```java
Intent intent = new Intent();
intent.setClass(A.this, B.class);
intent.putExtra("list", list);
startActivity(intent);
```
B.class:
```java
ArrayList<String>  mList = (ArrayList<String>) getIntent().getSerializableExtra("list");
```
第二种
```
Intent intent = new Intent(A.this, B.class);
intent.putStringArrayListExtra("mSelectedPhotos",mSelectedPhotos);
startActivity(intent);
					
//接受结果					
ArrayList<String> mSelectedPhotos = (ArrayList<String>) intent
						.getStringArrayListExtra("mSelectedPhotos");

```
## 构建 Intent
若activity中的intent filter满足以下intent对象的标准，系统就能够把特定的intent发送给activity：

Action:一个想要执行的动作的名称。通常是系统已经定义好的值，如ACTION_SEND或ACTION_VIEW。 在intent filter中通过<action>指定它的值，值的类型必须为字符串，而不是API中的常量(看下面的例子)

Data:Intent附带数据的描述。在intent filter中通过<data>指定它的值，可以使用一个或者多个属性，我们可以只定义MIME type或者是只指定URI prefix，也可以只定义一个URI scheme，或者是他们综合使用。

Note: 如果不想handle Uri 类型的数据，那么应该指定 android:mimeType 属性。例如 text/plain or image/jpeg.
Category:提供一个附加的方法来标识这个activity能够handle的intent。通常与用户的手势或者是启动位置有关。系统有支持几种不同的categories,但是大多数都很少用到。而且，所有的implicit intents都默认是 CATEGORY_DEFAULT 类型的。在intent filter中用<category>指定它的值。
```js
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
        <data android:mimeType="image/*"/>
    </intent-filter>
</activity>
```
## 强制使用应用选择器
## 接收隐式 Intent
## 过滤器示例
## 使用待定 Intent
## Intent 解析
## Intent 匹配
## 附录
[Intent and Intent Filters](https://developer.android.com/intl/zh-cn/guide/components/intents-filters.html)
[Intent](http://hukai.me/android-training-course-in-chinese/basics/intents/sending.html)