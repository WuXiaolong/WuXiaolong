title: github之Android-Iconics使用
date: 2015-07-29 
categories: [github]
tags: [Android,Iconics,github]
---
源码地址：[https://github.com/mikepenz/Android-Iconics](https://github.com/mikepenz/Android-Iconics)

* build.gradle添加

```java
dependencies {
    compile 'com.mikepenz:iconics:1.3.0@aar'
}
```
<!-- more -->

* 作为Drawable使用

```java
ImageView image = (ImageView) findViewById(R.id.image);
image.setImageDrawable(new IconicsDrawable(this, FontAwesome.Icon.faw_android).color(Color.RED).sizeDp(24));
```

* 作为MenuItem使用

```java
MenuItem menuItem = menu.findItem(R.id.action_opensource);
menuItem.setIcon(new IconicsDrawable(this, FontAwesome.Icon.faw_github).actionBar().color(Color.WHITE));
```

* xml使用

```js
 <com.mikepenz.iconics.view.IconicsImageView
        android:id="@+id/icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:clickable="false"
        android:padding="8dp"
        android:scaleType="fitXY"
        app:iiv_color="@android:color/holo_green_dark"
        app:iiv_icon="faw_adjust" />
```

```java
 IconicsImageView icon = (IconicsImageView) findViewById(R.id.icon);
  List<String> icons = new ArrayList<String>();
                        for (ITypeface font : Iconics.getRegisteredFonts()) {
                            Log.d("wxl", "FontName=" + font.getFontName());
                            if (font.getFontName().equalsIgnoreCase("FontAwesome")) {
                                for (String icon : font.getIcons()) {
                                    Log.d("wxl", "getIcons=" + icon);
                                    icons.add(icon);
                                }
                            }
                        }
  icon.setIcon(icons.get(0));
  
```
