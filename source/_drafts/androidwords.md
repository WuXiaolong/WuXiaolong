title: Android词汇
date: 2014-10-29 14:55:47
tags: Android
category: Android
---
>关于Android一些只言片语……

## Activity生命周期
* onCreate()
* onStart()
* onResume()
* onPause()
* onStop()
* onDestroy()
* onRestart()
* onSaveInstanceState()
通常来说，跳转到其他的activity或者是点击Home都会导致当前的activity执行onSaveInstanceState
* onRestoreInstanceState()
在 onStart() 方法之后执行. 系统仅仅会在存在需要恢复的状态信息时才会调用 onRestoreInstanceState() 
<!--more-->
## Fragment
为了执行fragment的增加或者移除操作，必须通过调用 getSupportFragmentManager()方法获取FragmentManager，
然后调用 beginTransaction() 方法创建一个FragmentTransaction对象, 
FragmentTransaction提供了用来增加、移除、替换以及其它一些操作。

## File
* internal 内存
* external SD card
* Environment.getExternalStorageState()

## 
* explicit 显式
* implicit 隐式
* startActivityForResult() 

## AsyncTask 
* doInBackground()
* onPostExecute()

## 网络操作
* HttpURLConnection
* Apache HttpClient
* InputStream
* Bitmap bitmap = BitmapFactory.decodeStream(inputStream);
* InputStreamReader
* ConnectivityManager 
* NetworkInfo 
* NetworkReceiver 

## 解析 XML 数据
* XmlPullParser


