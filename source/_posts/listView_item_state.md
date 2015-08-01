title: 刷新RecyclerView/ListView某个item状态
date: 2015-07-08
categories: [Android]
tags: [Android,SQLite]
---

### 1、比如列表有收藏按钮，当前页面收藏

```java
private List<Map<String, String>> mList = new ArrayList<>();
...
走收藏接口，在成功后
  mList.get(position).put("favorites", "1");
  notifyDataSetChanged();
```

<!-- more -->

### 2、比如列表有收藏按钮，下个页面进行收藏

这种情况下，收藏成功后，返回需要刷新上一个页面状态

DataAdapter

```java
 Intent intent = new Intent(activity, NextActivity.class); 
 intent.putExtra("userhead", doctorList.get(position) .getHeadImg()); 
 intent.putExtra("username", doctorList.get(position) .getName()); 
 intent.putExtra("userId", doctorList.get(position) .getUserinfo_djid()); 
 intent.putExtra("position", position); startActivityForResult(intent,AppConfig.REQUEST_CODE_DIALOGUE);
```

NextActivity.class

```java
收藏成功后，返回
Intent intent;
intent = new Intent();
Bundle bundle = new Bundle();
bundle.putInt("position", position);
intent.putExtras(bundle);
setResult(RESULT_OK, intent);
 finish();
```

onActivityResult

```java
public void onActivityResult(int requestCode, int resultCode, Intent data) {
            if (requestCode == AppConfig.REQUEST_CODE_DIALOGUE
                && resultCode == RESULT_OK) {
            int position = data.getExtras().getInt("position");
            Object object = dataAdapter.getItem(position);
            if (object instanceof DoctorList) {
                DoctorList doctorList = (DoctorList) object;
                doctorList.setUnreadnum("0");
                dataAdapter.notifyDataSetChanged();
            }
        }
        super.onActivityResult(requestCode, resultCode, data);
    }
```

DoctorList

```java
class DialogueBean {
    private String result;
    private ArrayList<DoctorList> doctorList;
public class DoctorList implements Serializable {
        private String userinfo_djid;
        private String headImg;
        private String name;
        private String unreadnum;
        private String infoContent;
        private String sendDate;
        private String ky_flag;
        private String tobewritten;
}
}
```

### 3、以上只是未重新请求接口下刷新View，未真正局部刷新 

* ListView局部刷新

```java
   private List<Map<String, Object>> mData;
   private ListView mListView;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mData = getData();
        MyAdapter adapter = new MyAdapter(this);
        mListView = (ListView) findViewById(R.id.listView);
        mListView.setAdapter(adapter);
    }

    private List<Map<String, Object>> getData() {
        List<Map<String, Object>> list = new ArrayList<>();
        Map<String, Object> map;
        for (int i = 0; i < 50; i++) {
            map = new HashMap<>();
            map.put("title", "G" + i);
            list.add(map);
        }
        return list;
    }


    public class MyAdapter extends BaseAdapter {
        private LayoutInflater mInflater;

        public MyAdapter(Context context) {
            this.mInflater = LayoutInflater.from(context);
        }

        @Override
        public int getCount() {
            return mData.size();
        }

        @Override
        public Object getItem(int arg0) {
            return mData.get(arg0);
        }

        @Override
        public long getItemId(int arg0) {
            return arg0;
        }

        @Override
        public View getView(final int position, View convertView, ViewGroup parent) {
            Log.d("wxl", "getView=" + position);
            ViewHolder holder = null;
            if (convertView == null) {
                holder = new ViewHolder();
                convertView = mInflater.inflate(R.layout.listview_item, null);
                holder.textview = (TextView) convertView.findViewById(R.id.textview);
                convertView.setTag(holder);
            } else {
                holder = (ViewHolder) convertView.getTag();
            }
            holder.textview.setText((String) mData.get(position).get("title"));
            convertView.setOnClickListener(new View.OnClickListener() {

                @Override
                public void onClick(View v) {
                    updateItemView(position);//状态改变时，调用，这里是为了演示方便
                }
            });


            return convertView;
        }

        public class ViewHolder {
            public TextView textview;
        }
    }

    public void updateItemView(int position) {
        //得到第一个可显示控件的位置，
        int visiblePosition = mListView.getFirstVisiblePosition();
        //只有当要更新的view在可见的位置时才更新，不可见时，跳过不更新
        int index = position - visiblePosition;
        if (index >= 0) {
            //得到要更新的item的view
            View view = mListView.getChildAt(index);
            //从view中取得holder
            MyAdapter.ViewHolder holder = (MyAdapter.ViewHolder) view.getTag();
            holder.textview.setText("测试数据");
            mData.get(index).put("title", "测试数据");
        }
    }
}
```
listview_item.xml

```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/textview"
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:layout_gravity="center_vertical"
        android:gravity="center_vertical" />
</LinearLayout>
```

* RecyclerView局部刷新

RecyclerView已经替代了ListView，局部刷新很有必要知道

```java
收藏成功后
mList.get(position).put("favorites", "0");
ViewHolder viewHolder = (ViewHolder) mRecyclerView.findViewHolderForAdapterPosition(position);
viewHolder.mCheck.setBackgroundResource(R.mipmap.collect_normal);
```