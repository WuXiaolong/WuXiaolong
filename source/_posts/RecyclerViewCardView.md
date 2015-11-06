title: RecyclerView+CardView使用
date: 2015-09-15 13:21:15
tags: [Android,RecyclerView,CardView]
category: Android
---
>RecyclerView和CardView使用

## 实例App
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyanScreenshots.gif)

<!--more-->

## gradle文件
```js
 compile 'com.android.support:recyclerview-v7:22.2.0'
 compile 'com.android.support:cardview-v7:22.2.0'
```
## XML文件
```js
 <android.support.v7.widget.RecyclerView
  android:id="@+id/mRecyclerView"
  android:layout_width="match_parent"
  android:layout_height="match_parent" />
```
## Activity中
```java
 mRecyclerView = (RecyclerView) view.findViewById(R.id.mRecyclerView);
  mRecyclerView.setLayoutManager(new LinearLayoutManager(this));//这里用线性显示 类似于listview
//mRecyclerView.setLayoutManager(new GridLayoutManager(this, 2));//这里用线性宫格显示 类似于grid view
//mRecyclerView.setLayoutManager(new StaggeredGridLayoutManager(2, OrientationHelper.VERTICAL));//这里用线性宫格显示 类似于瀑布流
  mRecyclerView.setAdapter(new DayTextAdapter(getActivity(), parselist));
```
## DayTextAdapter
```java
public class DayTextAdapter extends RecyclerView.Adapter<DayTextAdapter.ViewHolder> {
    List<Map<String, String>> parselist;
    private Activity activity;

    public List<Map<String, String>> getParselist() {
        return parselist;
    }

    public DayTextAdapter(Activity activity, List<Map<String, String>> parselist) {
        this.activity = activity;
        this.parselist = parselist;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.day_text_item, parent, false);
        return new ViewHolder(v);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        holder.title.setText(Html.fromHtml("<b>" + parselist.get(position).get("title") + "</b>"));
        holder.content.setText(Html.fromHtml("发布时间："
                + parselist.get(position).get("date") + "<br>"
                + parselist.get(position).get("postContent")));

    }

    @Override
    public int getItemCount() {
        return parselist.size();
    }

    public class ViewHolder extends RecyclerView.ViewHolder {
        TextView title, content;

        public ViewHolder(View itemView) {
            super(itemView);
            title = (TextView) itemView.findViewById(R.id.title);
            content = (TextView) itemView.findViewById(R.id.content);


            itemView.setOnClickListener(new View.OnClickListener() {

                public void onClick(View v) {
                   //点击事件
                }
            });
        }
    }
}
```
## day_text_item.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
<android.support.v7.widget.CardView 
    android:id="@+id/card_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    android:layout_marginBottom="@dimen/card_view_margin_bottom"
    android:layout_marginLeft="10dp"
    android:layout_marginRight="10dp"
    android:layout_marginTop="10dp"
	android:foreground="?attr/selectableItemBackground"
    card_view:cardBackgroundColor="?attr/itemBackgroundColor"
    card_view:cardCornerRadius="10dp"
    card_view:cardElevation="5dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:padding="15dip">

        <TextView
            android:id="@+id/title"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:textColor="?attr/titleColor"
            android:textSize="@dimen/title" />

        <TextView
            android:layout_width="fill_parent"
            android:layout_height="1dip"
            android:layout_marginTop="10dip"
            android:background="?attr/lineColor" />

        <TextView
            android:id="@+id/content"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dip"
            android:ellipsize="end"
            android:lines="7"
            android:textColor="?attr/contentColor"
            android:textSize="@dimen/content" />

    </LinearLayout>
</android.support.v7.widget.CardView>

</LinearLayout>
```
说明：
CardView中android:layout_marginBottom发现在5.0以上和以下效果不一样的。
res文件下values文件dimens.xml
```js
<resources>
    <dimen name="card_view_margin_bottom">0dp</dimen>
</resources>
```
再在res文件下建values-v21文件，并在dimens.xml
```js
<resources>
    <dimen name="card_view_margin_bottom">10dp</dimen>
</resources>
```
CardView参数：
```js
<resources>
    <declare-styleable name="CardView">
        <!-- Background color for CardView. -->
        <!-- 背景色 -->
        <attr name="cardBackgroundColor" format="color" />
        <!-- Corner radius for CardView. -->
        <!-- 边缘弧度数 -->
        <attr name="cardCornerRadius" format="dimension" />
        <!-- Elevation for CardView. -->
        <!-- 高度 -->
        <attr name="cardElevation" format="dimension" />
        <!-- Maximum Elevation for CardView. -->
        <!-- 最大高度 -->
        <attr name="cardMaxElevation" format="dimension" />
        <!-- Add padding in API v21+ as well to have the same measurements with previous versions. -->
        <!-- 设置内边距，v21+的版本和之前的版本仍旧具有一样的计算方式 -->
        <attr name="cardUseCompatPadding" format="boolean" />
        <!-- Add padding to CardView on v20 and before to prevent intersections between the Card content and rounded corners. -->
        <!-- 在v20和之前的版本中添加内边距，这个属性是为了防止卡片内容和边角的重叠 -->
        <attr name="cardPreventCornerOverlap" format="boolean" />
        <!-- 下面是卡片边界距离内部的距离-->
        <!-- Inner padding between the edges of the Card and children of the CardView. -->
        <attr name="contentPadding" format="dimension" />
        <!-- Inner padding between the left edge of the Card and children of the CardView. -->
        <attr name="contentPaddingLeft" format="dimension" />
        <!-- Inner padding between the right edge of the Card and children of the CardView. -->
        <attr name="contentPaddingRight" format="dimension" />
        <!-- Inner padding between the top edge of the Card and children of the CardView. -->
        <attr name="contentPaddingTop" format="dimension" />
        <!-- Inner padding between the bottom edge of the Card and children of the CardView. -->
        <attr name="contentPaddingBottom" format="dimension" />
    </declare-styleable>
</resources>
```