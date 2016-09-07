title: github之Font-Awesome使用
date: 2015-10-08 15:31:38
tags: Font-Awesome
category: Library
---
>github地址：[https://github.com/FortAwesome/Font-Awesome](https://github.com/FortAwesome/Font-Awesome)，一直知道这个项目，但是却不知道如何使用它，现在有个[博客](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0925/3518.html)介绍，感谢博主。我这里做个记录。

## 导入字体文件
在安卓项目中，导航到 app > src > main。 main 目录应该包含了一个叫 assets的文件夹。在assets 文件夹中创建另一个fonts文件夹，并把fontawesome-webfont.ttf 添加到这个文件夹。
fonts 文件夹并不是必须的。你可以直接把FontAwesome 的字体文件放在 assets 目录，但是把相同类型的文件放在专门的目录里面比较方便。只要FontAwesome 字体在assets 或者子目录之下就行。
<!--more-->
## 创建一个帮助类
```java
public class FontManager {
    public static final String ROOT = "",
            FONTAWESOME = ROOT + "fontawesome-webfont.ttf";

    public static Typeface getTypeface(Context context, String font) {
        return Typeface.createFromAsset(context.getAssets(), font);
    }
  /**
     * 图标一般都是包含在一个ViewGroup，
     * 比如一个RelativeLayout或者LinearLayout中。
     * 我们可以写一个方法，爬遍指定xml parent 并且递归的覆盖每个TextView的字体。
     *
     */
    public static void markAsIconContainer(View v, Typeface typeface) {
        if (v instanceof ViewGroup) {
            ViewGroup vg = (ViewGroup) v;
            for (int i = 0; i < vg.getChildCount(); i++) {
                View child = vg.getChildAt(i);
                markAsIconContainer(child,typeface);
            }
        } else if (v instanceof TextView) {
            ((TextView) v).setTypeface(typeface);
        }
    }


}
```
## 使用图标
访问 FontAwesome的GitHub页面 并浏览所给的图标。选择三个你喜欢的。

进入 values 文件夹并创建一个新的文件：icons.xml。这个文件将被作为字典使用，它将把Unicode 字符和相应的图标用可读的名字匹配起来。这意味着我们需要为每个图标创建一个入口。
```js
<resources>
    <string name="fa_icon_areachart">&#xf1fe;</string>
    <string name="fa_icon_piechart">&#xf200;</string>
    <string name="fa_icon_linechart">&#xf201;</string>
</resources>
```
onCreate方法
```java
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_font_awesome);
        Typeface iconFont = FontManager.getTypeface(FontAwesomeActivity.this, FontManager.FONTAWESOME);
        FontManager.markAsIconContainer(findViewById(R.id.icons_container), iconFont);
    }
```
对应的activity_font_awesome.xml
```js
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/icons_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center"
        android:text="@string/fa_icon_areachart" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center"
        android:text="@string/fa_icon_piechart"
        android:textColor="#9b59b6"
        android:textSize="45sp" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center"
        android:text="@string/fa_icon_linechart" />

</LinearLayout>
```