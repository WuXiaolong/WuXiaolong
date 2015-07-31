title: Android之CustomToast
date: 2015-04-17 
categories: [Android]
tags: [Android]
---

一、简单的CustomToast

Java

```java
public class CustomToast {
	private String tip = "";
	private Activity activity;
	private int duration;

	public CustomToast(Activity activity, String tip, int duration) {
		this.tip = tip;
		this.activity = activity;
		this.duration = duration;
	}

	@SuppressLint("InflateParams")
	public void show() {
		LayoutInflater inflater = activity.getLayoutInflater();
		View view = inflater.inflate(R.layout.custom_toast, null);
		TextView txt = (TextView) view.findViewById(R.id.txt);
		txt.setText(tip);
		Toast toast = new Toast(activity);
		// toast.setGravity(Gravity.CENTER | Gravity.TOP, 12, 40);
		toast.setDuration(duration);
		toast.setView(view);
		toast.show();
	}
}
```

<!-- more -->

custom_toast.xml

```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/shape_toast"
        android:orientation="vertical" >

        <TextView
            android:id="@+id/txt"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:padding="5dp"
            android:textColor="@android:color/white"
            android:textSize="@dimen/text_size_medium" />
    </LinearLayout>

</LinearLayout>
```

调用

```java
new CustomToast(WelcomeActivity.this,"番茄-视频专辑app，下载地址：http://app.mi.com/detail/65621",Toast.LENGTH_SHORT).show();
```

二、重写继承Toast

Java

```java
public class TipsToast extends Toast {

    public TipsToast(Context context) {
        super(context);
    }

    @SuppressLint("InflateParams")
	public static TipsToast makeText(Context context, CharSequence text, int duration) {
        TipsToast result = new TipsToast(context);

        LayoutInflater inflate = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        View v = inflate.inflate(R.layout.view_tips, null);
        TextView tv = (TextView) v.findViewById(R.id.tips_msg);
        TextView msgTxt = (TextView) v.findViewById(R.id.tips_msg_txt);
        msgTxt.setVisibility(View.GONE);
        tv.setText(text);

        result.setView(v);
        // setGravity方法用于设置位置，此处为垂直居中
        result.setGravity(Gravity.CENTER_VERTICAL, 0, 0);
        result.setDuration(duration);

        return result;
    }

    @SuppressLint("InflateParams")
   	public static TipsToast makeText(Context context, CharSequence text, CharSequence msg, int duration) {
           TipsToast result = new TipsToast(context);

           LayoutInflater inflate = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
           View v = inflate.inflate(R.layout.view_tips, null);
           TextView tv = (TextView) v.findViewById(R.id.tips_msg);
           TextView msgTxt = (TextView) v.findViewById(R.id.tips_msg_txt);
           tv.setText(text);
           msgTxt.setVisibility(View.VISIBLE);
           msgTxt.setText(msg);

           result.setView(v);
           // setGravity方法用于设置位置，此处为垂直居中
           result.setGravity(Gravity.CENTER_VERTICAL, 0, 0);
           result.setDuration(duration);

           return result;
       }
    
    
    public static TipsToast makeText(Context context, int resId, int duration) throws Resources.NotFoundException {
        return makeText(context, context.getResources().getText(resId), duration);
    }

    public void setIcon(int iconResId) {
        if (getView() == null) {
            throw new RuntimeException("This Toast was not created with Toast.makeText()");
        }
        ImageView iv = (ImageView) getView().findViewById(R.id.tips_icon);
        if (iv == null) {
            throw new RuntimeException("This Toast was not created with Toast.makeText()");
        }
        iv.setImageResource(iconResId);
    }
    @Override
    public void setText(CharSequence s) {
        if (getView() == null) {
            throw new RuntimeException("This Toast was not created with Toast.makeText()");
        }
        TextView tv = (TextView) getView().findViewById(R.id.tips_msg);
        if (tv == null) {
            throw new RuntimeException("This Toast was not created with Toast.makeText()");
        }
        tv.setText(s);
    }
}
```

view_tips.xml

```js
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    android:background="@drawable/tips_bg" >

    <!-- 撑开布局 -->
    <View
        android:layout_width="170dp"
        android:layout_height="150dp"
        
        android:layout_centerInParent="true" />

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:orientation="vertical" >

        <ImageView
            android:id="@+id/tips_icon"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:layout_marginBottom="10.0dip"
            android:gravity="center" />

        <TextView
            android:id="@+id/tips_msg"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:maxWidth="180dip"
            android:layout_gravity="center"
            android:gravity="center"
            android:lineSpacingExtra="3.0dip"
            android:textColor="#ffffffff"
            android:textSize="17.0sp" />
        
        <TextView
            android:id="@+id/tips_msg_txt"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:gravity="center"
            android:lineSpacingExtra="3.0dip"
            android:textColor="#ffffffff"
            android:textSize="17.0sp"
            android:visibility="gone" />
    </LinearLayout>

</RelativeLayout>
```

调用

```java
TipsToast tipsToast = TipsToast.makeText(context.getApplication().getBaseContext(),"今日情感app，下载地址：http://app.mi.com/detail/30499",TipsToast.LENGTH_SHORT);
tipsToast.setIcon(R.drawable.warn);
tipsToast.show();
	
```
