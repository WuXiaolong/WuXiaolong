title: 微言之自定义分享PopupWindow
date: 2015-09-02 09:54:41
tags: [Android,PopupWindow,WeiYan]
category: Android
---

>自定义分享PopupWindow，有需要的直接拿走。

## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyanSharePopupWindowScreenshots.gif)

<!--more-->

## 应用实例
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyanAppDownload.png)

## 核心代码
### SharePopupWindow代码
```java
public class SharePopupWindow extends PopupWindow {
    private View mMenuView;
    private Activity mActivity;
    private String mShareContent;
    private View mParent;
    private Bitmap mGetSaveBitmap;

    public SharePopupWindow(Activity activity, String shareContent, View parent) {
        super(activity);
        this.mActivity = activity;
        this.mShareContent = shareContent;
        this.mParent = parent;
        initView();
    }

    private void initView() {
        mMenuView = View.inflate(mActivity, R.layout.share_popup, null);
        // 设置按钮监听
        mMenuView.findViewById(R.id.btn_friend).setOnClickListener(shareItemsOnClick);
        mMenuView.findViewById(R.id.btn_quan).setOnClickListener(shareItemsOnClick);
        mMenuView.findViewById(R.id.btn_collection).setOnClickListener(shareItemsOnClick);
        mMenuView.findViewById(R.id.btn_qq).setOnClickListener(shareItemsOnClick);
        mMenuView.findViewById(R.id.btn_qzone).setOnClickListener(shareItemsOnClick);
        mMenuView.findViewById(R.id.btn_sina).setOnClickListener(shareItemsOnClick);
        mMenuView.findViewById(R.id.btn_tx).setOnClickListener(shareItemsOnClick);
        mMenuView.findViewById(R.id.btn_cancel).setOnClickListener(
                new View.OnClickListener() {

                    @Override
                    public void onClick(View v) {
                        dismiss();

                    }
                });

        // 设置SelectPicPopupWindow的View
        this.setContentView(mMenuView);
        // 设置SelectPicPopupWindow弹出窗体的宽
        this.setWidth(LayoutParams.MATCH_PARENT);
        // 设置SelectPicPopupWindow弹出窗体的高
        this.setHeight(LayoutParams.WRAP_CONTENT);
        // 设置SelectPicPopupWindow弹出窗体可点击
        this.setFocusable(true);
        // 设置SelectPicPopupWindow弹出窗体动画效果
        // this.setAnimationStyle(R.style.AnimBottom);
        // 实例化一个ColorDrawable颜色为半透明
        ColorDrawable dw = new ColorDrawable(00000000);
        // 设置SelectPicPopupWindow弹出窗体的背景
        this.setBackgroundDrawable(dw);
        this.showAtLocation(mParent, Gravity.BOTTOM | Gravity.CENTER_HORIZONTAL, 0, 0);
        // mMenuView添加OnTouchListener监听判断获取触屏位置如果在选择框外面则销毁弹出框
        mMenuView.setOnTouchListener(new OnTouchListener() {

            public boolean onTouch(View v, MotionEvent event) {

                int height = mMenuView.findViewById(R.id.pop_layout).getTop();
                int y = (int) event.getY();
                if (event.getAction() == MotionEvent.ACTION_UP) {
                    if (y < height) {
                        dismiss();
                    }
                }
                return true;
            }
        });

    }


    /**
     * 为弹出窗口实现监听类
     */
    private View.OnClickListener shareItemsOnClick = new View.OnClickListener() {

        public void onClick(View v) {


            switch (v.getId()) {
                case R.id.btn_friend:
                   //具体分享需要自己写
                    break;
                case R.id.btn_quan:
                   
                    break;
                case R.id.btn_collection:
                   
                    break;
                case R.id.btn_qq:
                  
                    break;
                case R.id.btn_qzone:
                  
                    break;
                case R.id.btn_sina:
                  
                    break;
                case R.id.btn_tx:
               
                    break;
                default:
                    break;
            }

        }

    };
}
```

### share_popup.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:background="#50000000"
    android:gravity="center_horizontal"
    android:orientation="vertical">

    <LinearLayout
        android:id="@+id/pop_layout"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:background="@android:color/white"
        android:orientation="vertical">

        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:layout_marginLeft="22dp"
            android:layout_marginRight="22dp"
            android:layout_marginTop="10dp"
            android:background="@android:color/white"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/btn_friend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:drawableTop="@drawable/umeng_socialize_wechat"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="微信好友"
                android:textColor="@color/txt_black" />

            <TextView
                android:id="@+id/btn_quan"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="10dp"
                android:layout_weight="1"
                android:drawableTop="@drawable/umeng_socialize_wxcircle"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="朋友圈"
                android:textColor="@color/txt_black" />

            <TextView
                android:id="@+id/btn_collection"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="10dp"
                android:layout_weight="1"
                android:drawableTop="@drawable/wx_collect"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="微信收藏"
                android:textColor="@color/txt_black" />

            <TextView
                android:id="@+id/btn_qq"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="10dp"
                android:layout_weight="1"
                android:drawableTop="@drawable/umeng_socialize_qq_on"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="QQ"
                android:textColor="@color/txt_black" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:layout_marginLeft="22dp"
            android:layout_marginRight="22dp"
            android:layout_marginTop="10dp"
            android:background="@android:color/white"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/btn_sina"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:drawableTop="@drawable/umeng_socialize_sina_on"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="新浪微博"
                android:textColor="@color/txt_black" />

            <TextView
                android:id="@+id/btn_qzone"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="10dp"
                android:layout_weight="1"
                android:drawableTop="@drawable/umeng_socialize_qzone_on"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="QQ空间"
                android:textColor="@color/txt_black" />


            <TextView
                android:id="@+id/btn_tx"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="10dp"
                android:layout_weight="1"
                android:drawableTop="@drawable/umeng_socialize_tx_on"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="腾讯微博"
                android:textColor="@color/txt_black"
                android:visibility="invisible" />

            <TextView
                android:id="@+id/btn_more"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginLeft="10dp"
                android:layout_weight="1"
                android:gravity="center_horizontal"
                android:onClick="shareItemsOnClick"
                android:text="更多分享"
                android:textColor="@color/txt_black"
                android:visibility="invisible" />
        </LinearLayout>

        <Button
            android:id="@+id/btn_cancel"
            android:layout_width="fill_parent"
            android:layout_height="45dp"
            android:layout_marginBottom="10dp"
            android:layout_marginLeft="22dp"
            android:layout_marginRight="22dp"
            android:layout_marginTop="10dp"
            android:background="@drawable/shape_round_gray"
            android:gravity="center_horizontal|center_vertical"
            android:onClick="shareItemsOnClick"
            android:text="@string/cancel"
            android:textColor="@android:color/white" />
    </LinearLayout>

</RelativeLayout>
```

### SharePopupWindow调用
```java
new SharePopupWindow(ContentActivity.this, shareContent, ContentActivity.this.findViewById(R.id.content_layout));            
```
第三个参数，是需要的分享页面的根ID，如：
```js
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fab="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/content_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/bg"
    tools:context="com.android.xiaomolongstudio.hua.activity.JokeTextDetailActivity">
</RelativeLayout>
```
