title: Android更换主题
date: 2015-08-19 11:05:53
tags: [Android,Theme]
category: Android
---
>来自开源项目：[https://github.com/lguipeng/Notes](https://github.com/lguipeng/Notes)，其中有改变主题的功能，我做了部分修改，并应用到我的APP中。感谢lguipeng的开源。

## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/HuaChangeThemeScreenshots.gif)
<!--more-->

## 实例APK
[魅族应用中心](http://app.meizu.com/apps/public/detail?package_name=com.android.xiaomolongstudio.hua)

[360手机助手](http://zhushou.360.cn/detail/index/soft_id/759825?recrefer=SE_D_%E8%8A%B1%E5%A7%91%E5%A8%98)

## 颜色定义
color.xml
```js
 <color name="red_primary">#F44336</color>
    <color name="red_primary_dark">#D32F2F</color>
    <color name="red_accent">#F44336</color>

    <color name="pink_primary">#E91E63</color>
    <color name="pink_primary_dark">#C2185B</color>
    <color name="pink_accent">#E91E63</color>

    <color name="brown_primary">#795548</color>
    <color name="brown_primary_dark">#5D4037</color>
    <color name="brown_accent">#795548</color>


    <color name="blue_primary">#2196F3</color>
    <color name="blue_primary_dark">#1976D2</color>
    <color name="blue_accent">#2196F3</color>

    <color name="blue_grey_primary">#607D8B</color>
    <color name="blue_grey_primary_dark">#455A64</color>
    <color name="blue_grey_accent">#607D8B</color>

    <color name="yellow_primary">#FFEB3B</color>
    <color name="yellow_primary_dark">#FBC02D</color>
    <color name="yellow_accent">#FFEB3B</color>

    <color name="deep_purple_primary">#673AB7</color>
    <color name="deep_purple_primary_dark">#512DA8</color>
    <color name="deep_purple_accent">#673AB7</color>


    <color name="green_primary">#4CAF50</color>
    <color name="green_primary_dark">#388E3C</color>
    <color name="green_accent">#4CAF50</color>

    <color name="deep_orange_primary">#FF5722</color>
    <color name="deep_orange_primary_dark">#E64A19</color>
    <color name="deep_orange_accent">#FF5722</color>

    <color name="grey_primary">#9E9E9E</color>
    <color name="grey_primary_dark">#616161</color>
    <color name="grey_accent">#9E9E9E</color>

    <color name="cyan_primary">#00BCD4</color>
    <color name="cyan_primary_dark">#0097A7</color>
    <color name="cyan_accent">#00BCD4</color>

    <color name="amber_primary">#FFC107</color>
    <color name="amber_primary_dark">#FFA000</color>
    <color name="amber_accent">#FFC107</color>

    <color name="primary">#2196F3</color>
    <color name="primary_dark">#1E88E5</color>
    <color name="accent">@color/primary</color>
```

## 样式定义
style.xml
```js
 <style name="RedTheme" parent="AppTheme">
        <item name="colorPrimary">@color/red_primary</item>
        <item name="colorPrimaryDark">@color/red_primary_dark</item>
        <item name="colorAccent">@color/red_accent</item>
    </style>

    <style name="PinkTheme" parent="AppTheme">
        <item name="colorPrimary">@color/pink_primary</item>
        <item name="colorPrimaryDark">@color/pink_primary_dark</item>
        <item name="colorAccent">@color/pink_accent</item>
    </style>

    <style name="BrownTheme" parent="AppTheme">
        <item name="colorPrimary">@color/brown_primary</item>
        <item name="colorPrimaryDark">@color/brown_primary_dark</item>
        <item name="colorAccent">@color/brown_accent</item>
    </style>


    <style name="BlueTheme" parent="AppTheme">
        <item name="colorPrimary">@color/blue_primary</item>
        <item name="colorPrimaryDark">@color/blue_primary_dark</item>
        <item name="colorAccent">@color/blue_accent</item>
    </style>


    <style name="BlueGreyTheme" parent="AppTheme">
        <item name="colorPrimary">@color/blue_grey_primary</item>
        <item name="colorPrimaryDark">@color/blue_grey_primary_dark</item>
        <item name="colorAccent">@color/blue_grey_accent</item>
    </style>


    <style name="YellowTheme" parent="AppTheme">
        <item name="colorPrimary">@color/yellow_primary</item>
        <item name="colorPrimaryDark">@color/yellow_primary_dark</item>
        <item name="colorAccent">@color/yellow_accent</item>
    </style>


    <style name="DeepPurpleTheme" parent="AppTheme">
        <item name="colorPrimary">@color/deep_purple_primary</item>
        <item name="colorPrimaryDark">@color/deep_purple_primary_dark</item>
        <item name="colorAccent">@color/deep_purple_accent</item>
    </style>


    <style name="GreenTheme" parent="AppTheme">
        <item name="colorPrimary">@color/green_primary</item>
        <item name="colorPrimaryDark">@color/green_primary_dark</item>
        <item name="colorAccent">@color/green_accent</item>
    </style>

    <style name="DeepOrangeTheme" parent="AppTheme">
        <item name="colorPrimary">@color/deep_orange_primary</item>
        <item name="colorPrimaryDark">@color/deep_orange_primary_dark</item>
        <item name="colorAccent">@color/deep_orange_accent</item>
    </style>

    <style name="GreyTheme" parent="AppTheme">
        <item name="colorPrimary">@color/grey_primary</item>
        <item name="colorPrimaryDark">@color/grey_primary_dark</item>
        <item name="colorAccent">@color/grey_accent</item>
    </style>

    <style name="CyanTheme" parent="AppTheme">
        <item name="colorPrimary">@color/cyan_primary</item>
        <item name="colorPrimaryDark">@color/cyan_primary_dark</item>
        <item name="colorAccent">@color/cyan_accent</item>
    </style>

    <style name="AmberTheme" parent="AppTheme">
        <item name="colorPrimary">@color/amber_primary</item>
        <item name="colorPrimaryDark">@color/amber_primary_dark</item>
        <item name="colorAccent">@color/amber_accent</item>
    </style>
```

## 主题选择对话框
### 在对话框里加入了GridView
```java
 private void showThemeChooseDialog() {
        AlertDialog.Builder builder = new AlertDialog.Builder(getActivity(), R.style.ChangeThemeDialog);
        builder.setTitle("更换主题");
        Integer[] res = new Integer[]{R.drawable.red_round, R.drawable.brown_round, R.drawable.blue_round,
                R.drawable.blue_grey_round, R.drawable.yellow_round, R.drawable.deep_purple_round,
                R.drawable.pink_round, R.drawable.green_round, R.drawable.deep_orange_round,
                R.drawable.grey_round, R.drawable.cyan_round,R.drawable.amber_round};
        List<Integer> list = Arrays.asList(res);
        ColorsListAdapter adapter = new ColorsListAdapter(getActivity(), list);
        adapter.setCheckItem(ThemeUtils.getCurrentTheme(getActivity()).getIntValue());
        GridView gridView = (GridView) LayoutInflater.from(getActivity()).inflate(R.layout.colors_panel_layout, null);
        gridView.setStretchMode(GridView.STRETCH_COLUMN_WIDTH);
        gridView.setCacheColorHint(0);
        gridView.setAdapter(adapter);
        builder.setView(gridView);
        final AlertDialog dialog = builder.show();
        gridView.setOnItemClickListener(
                new AdapterView.OnItemClickListener() {
                    @Override
                    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                        dialog.dismiss();
                        int value = ThemeUtils.getCurrentTheme(getActivity()).getIntValue();
                        Log.d("wxl", "value==" + value);
                        if (value != position) {
                            preferenceUtils.saveParam(getString(R.string.change_theme_key), position);
                            changeTheme(ThemeUtils.Theme.mapValueToTheme(position));
                        }
                    }
                }

        );
    }
```
### 对话框圆形颜色
drawable下建red_round.xml，代码如下：
```js
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="oval">
    <solid android:color="@color/red_primary"/>
</shape>
```
### ColorsListAdapter 
GridView 的适配器和它的xml
```java
public class ColorsListAdapter extends BaseAdapter {

    private int checkItem;
    Context context;
    List<Integer> list;

    public ColorsListAdapter(Context context, List<Integer> list) {
        this.context = context;
        this.list = list;
    }


    @Override
    public int getCount() {
        return list.size();
    }

    @Override
    public Object getItem(int position) {
        return list.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        Holder holder;
        if (convertView == null) {
            convertView = LayoutInflater.from(context).inflate(R.layout.colors_image_layout, null);
            holder = new Holder();
            holder.imageView1 = (ImageView) convertView.findViewById(R.id.img_1);
            holder.imageView2 = (ImageView) convertView.findViewById(R.id.img_2);
            convertView.setTag(holder);
        } else {
            holder = (Holder) convertView.getTag();
        }
        holder.imageView1.setImageResource(list.get(position));
        if (checkItem == position) {
            holder.imageView2.setImageResource(R.drawable.ic_done_white);
        }
        return convertView;
    }
    public void setCheckItem(int checkItem) {
        this.checkItem = checkItem;
    }
    static class Holder {
        ImageView imageView1;
        ImageView imageView2;
    }
}
```

colors_image_layout.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:background="@android:color/transparent"
    android:orientation="vertical"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">
    <ImageView
        android:id="@+id/img_1"
        android:layout_gravity="center"
        android:layout_width="@dimen/md_simplelist_icon"
        android:layout_height="@dimen/md_simplelist_icon" />
    <ImageView
        android:id="@+id/img_2"
        android:layout_gravity="center"
        android:layout_width="20dp"
        android:layout_height="20dp" />
</FrameLayout>
```
### changeTheme方法
这里用的EventBus发布事件
```java
private void changeTheme(ThemeUtils.Theme theme) {
        if (getActivity() == null)
            return;              
       finish();         EventBus.getDefault().post(Constants.MainEvent.CHANGE_THEME);
       
    }

```
需要立即改变主题，调用如下代码：
```java
 public void onEventMainThread(Constants.MainEvent event) {
        switch (event) {
             case CHANGE_THEME:
                this.recreate();
               break;
        }
    }
```
### PreferenceUtils
SharedPreferences 封装类
```java
public class PreferenceUtils {

    private SharedPreferences sharedPreferences;

    private SharedPreferences.Editor shareEditor;

    private static PreferenceUtils preferenceUtils = null;

    public static final String NOTE_TYPE_KEY = "NOTE_TYPE_KEY";

    public static final String EVERNOTE_ACCOUNT_KEY = "EVERNOTE_ACCOUNT_KEY";

    public static final String EVERNOTE_NOTEBOOK_GUID_KEY = "EVERNOTE_NOTEBOOK_GUID_KEY";

    private PreferenceUtils(Context context){
        sharedPreferences = context.getSharedPreferences(SetFragment.PREFERENCE_FILE_NAME, Context.MODE_PRIVATE);
        shareEditor = sharedPreferences.edit();
    }

    public static PreferenceUtils getInstance(Context context){
        if (preferenceUtils == null) {
            synchronized (PreferenceUtils.class) {
                if (preferenceUtils == null) {
                    preferenceUtils = new PreferenceUtils(context.getApplicationContext());
                }
            }
        }
        return preferenceUtils;
    }

    public String getStringParam(String key){
        return getStringParam(key, "");
    }

    public String getStringParam(String key, String defaultString){
        return sharedPreferences.getString(key, defaultString);
    }

    public void saveParam(String key, String value)
    {
        shareEditor.putString(key,value).commit();
    }

    public boolean getBooleanParam(String key){
        return getBooleanParam(key, false);
    }

    public boolean getBooleanParam(String key, boolean defaultBool){
        return sharedPreferences.getBoolean(key, defaultBool);
    }

    public void saveParam(String key, boolean value){
        shareEditor.putBoolean(key, value).commit();
    }

    public int getIntParam(String key){
        return getIntParam(key, 0);
    }

    public int getIntParam(String key, int defaultInt){
        return sharedPreferences.getInt(key, defaultInt);
    }

    public void saveParam(String key, int value){
        shareEditor.putInt(key, value).commit();
    }

    public long getLongParam(String key){
        return getLongParam(key, 0);
    }

    public long getLongParam(String key, long defaultInt){
        return sharedPreferences.getLong(key, defaultInt);
    }

    public void saveParam(String key, long value){
        shareEditor.putLong(key, value).commit();
    }

    public void removeKey(String key){
        shareEditor.remove(key).commit();
    }
}

```
### ThemeUtils
```java
public class ThemeUtils {

    public static void changTheme(Activity activity, Theme theme) {
        if (activity == null)
            return;
        int style = R.style.RedTheme;
        switch (theme) {
            case BROWN:
                style = R.style.BrownTheme;
                break;
            case BLUE:
                style = R.style.BlueTheme;
                break;
            case BLUE_GREY:
                style = R.style.BlueGreyTheme;
                break;
            case YELLOW:
                style = R.style.YellowTheme;
                break;
            case DEEP_PURPLE:
                style = R.style.DeepPurpleTheme;
                break;
            case PINK:
                style = R.style.PinkTheme;
                break;
            case GREEN:
                style = R.style.GreenTheme;
                break;
            case DEEP_ORANGE:
                style = R.style.DeepOrangeTheme;
                break;
            case GREY:
                style = R.style.GreyTheme;
                break;
            case CYAN:
                style = R.style.CyanTheme;
                break;
            case AMBER:
                style = R.style.AmberTheme;
                break;
            default:
                break;
        }
        activity.setTheme(style);
    }

    public static Theme getCurrentTheme(Context context) {
        int value = PreferenceUtils.getInstance(context)
                .getIntParam(context.getString(R.string.change_theme_key), 0);
        return ThemeUtils.Theme.mapValueToTheme(value);
    }

    public enum Theme {
        RED(0),
        BROWN(1),
        BLUE(2),
        BLUE_GREY(3),
        YELLOW(4),
        DEEP_PURPLE(5),
        PINK(6),
        GREEN(7),
        DEEP_ORANGE(8),
        GREY(9),
        CYAN(10),
        AMBER(11);

        private int mValue;

        Theme(int value) {
            this.mValue = value;
        }

        public static Theme mapValueToTheme(final int value) {
            for (Theme theme : Theme.values()) {
                if (value == theme.getIntValue()) {
                    return theme;
                }
            }
            // If run here, return default
            return RED;
        }

        static Theme getDefault() {
            return RED;
        }

        public int getIntValue() {
            return mValue;
        }
    }
}
```

## 基类BaseActivity
初始化主题
```java
public class BaseActivity extends AppCompatActivity {
    protected PreferenceUtils preferenceUtils;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        preferenceUtils = PreferenceUtils.getInstance(this);
        initTheme();
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_base);
    }

    private void initTheme() {
        ThemeUtils.Theme theme = ThemeUtils.getCurrentTheme(this);
        ThemeUtils.changTheme(this, theme);
    }

   }
```