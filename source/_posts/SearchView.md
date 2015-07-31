title: Actionbar之SearchView
date: 2014-12-11 
categories: [Android]
tags: [Android,Actionbar,SearchView]
---

```js
 <item
        android:id="@+id/menu_search"
        android:actionLayout="@layout/searchview"//自定义
        android:icon="@drawable/ic_action_search"
        android:showAsAction="always|collapseActionView"
        android:title="Search"/>
        android:actionViewClass="android.widget.SearchView"//系统的
```
我们设定 showAsAction这个属性的时候，加了collapseActionView这么一个参数，它的意思是说，将item引用的布局隐藏起来，当你点击该item的时候，再将其展现出来。通过这个属性，我们就可以将每个item复杂的布局隐藏起来，从而有效的节约了actionbar的布局空间。
<!-- more -->
searchview.xml

```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <EditText
        android:id="@+id/search"
        style="@android:style/Widget.Holo.EditText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:hint="搜索视频"
        android:imeOptions="actionSearch"
        android:singleLine="true"
        android:textColor="@android:color/black"
        android:textCursorDrawable="@null" />

</LinearLayout>
```

代码

```java
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {

		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.activity_main, menu);
		initSearch(menu);
		return true;
	}

	private VideoFragment videoFragment;

	/**
	 * 搜索动作
	 * 
	 * @param menu
	 */
	private void initSearch(Menu menu) {
		MenuItem item = menu.findItem(R.id.menu_search);
		View view = item.getActionView();
		final EditText search = (EditText) view.findViewById(R.id.search);
		MenuItemCompat.setOnActionExpandListener(item,
				new OnActionExpandListener() {
					@Override
					public boolean onMenuItemActionCollapse(MenuItem item) {
						// Toast.makeText(MainDrawerActivity.this, "啊哦，我隐藏起来了！",
						// Toast.LENGTH_SHORT).show();
						search.clearFocus(); // 清除焦点
						AppUtil.hideSoftInput(MainDrawerActivity.this);
						if (search.getText().length() > 0) {// 重新恢复搜索之前的数据
							videoFragment.searchVideoTable(false, "");//
						}
						return true;
					}

					@Override
					public boolean onMenuItemActionExpand(MenuItem item) {
						// Toast.makeText(MainDrawerActivity.this, "啦啦啦，我出现喽！",
						// Toast.LENGTH_SHORT).show();
						search.requestFocus(); // 请求获取焦点
						/**
						 * 调起软键盘
						 */
						InputMethodManager m = (InputMethodManager)     getSystemService(Context.INPUT_METHOD_SERVICE);
						m.toggleSoftInput(0, InputMethodManager.HIDE_NOT_ALWAYS);

						return true;
					}
				});
		search.setOnEditorActionListener(new OnEditorActionListener() {

			@Override
			public boolean onEditorAction(TextView v, int actionId,
					KeyEvent event) {
				if (actionId == EditorInfo.IME_ACTION_SEARCH) {
					if (search.getText().length() > 0) {// 搜索
						AppUtil.hideSoftInput(MainDrawerActivity.this);
						videoFragment.searchVideoTable(true, search.getText()
								.toString());
					}
					return true;
				}
				return false;
			}
		});
	}
```

* 参考

[Android ActionBar 的其它用法（搜索、分享、隐藏复杂布局，模仿Google Play,微信）](http://blog.csdn.net/xy_nyle/article/details/18894755)

[Android在EditText中开启软键盘的＂Done＂按钮](http://www.fengfly.com/plus/view-206564-1.html)

[输入法软键盘显示/隐藏的监听和控制](http://glblong.blog.51cto.com/3058613/1543172)

[修改Android EditText光标颜色](http://blog.csdn.net/cw2004100021124/article/details/12649671)

[Android:自定义输入框光标颜色](http://my.oschina.net/xsjayz/blog/104344)
