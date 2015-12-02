title: Android Lollipop新特性-Palette
date: 2015-08-03 11:19:09
tags: [Android,Palette]
category: Android
---
## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/paletteScreenshots.gif)
<!-- more -->
## 使用方法
Palette可以提取的颜色如下
* Vibrant （有活力的）
* Vibrant dark（有活力的 暗色）
* Vibrant light（有活力的 亮色）
* Muted （柔和的）
* Muted dark（柔和的 暗色）
* Muted light（柔和的 亮色）

我们要想使用Palette，需要导入Palette的兼容库，Gradle 中添加下面依赖。
```js
compile 'com.android.support:palette-v7:21.0.+'
```

## 实例代码
xml
```js
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context=".MainActivity">

    <ImageView
        android:id="@+id/image"
        android:layout_width="72dp"
        android:layout_height="72dp" />

    <Button
        android:id="@+id/vibrant"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="vibrant" />

    <Button
        android:id="@+id/vibrantLight"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="vibrantLight" />

    <Button
        android:id="@+id/vibrantDark"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="vibrantDark" />

    <Button
        android:id="@+id/muted"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="muted" />

    <Button
        android:id="@+id/mutedLight"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="mutedLight" />

    <Button
        android:id="@+id/mutedDark"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="onClick"
        android:text="mutedDark" />


</LinearLayout>

```

java

```java
package com.wuxiaolong.apksample;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.drawable.BitmapDrawable;
import android.graphics.drawable.ColorDrawable;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.support.v7.app.ActionBar;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.graphics.Palette;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.ImageView;


public class MainActivity extends AppCompatActivity {
    ImageView image;
    ActionBar mActionBar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);


        image = (ImageView) findViewById(R.id.image);
        image.setImageResource(R.mipmap.dog);
        final Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.dog);
        mActionBar = getSupportActionBar();


    }

    public void onClick(View view) {
        Palette.from(drawableToBitmap(image.getDrawable())).generate(new LPaletteAsyncListener(view));
    }

    /**
     * 参考博客https://www.bignerdranch.com/blog/extracting-colors-to-a-palette-with-android-lollipop/
     */
    class LPaletteAsyncListener implements Palette.PaletteAsyncListener {
        View view;

        LPaletteAsyncListener(View view) {
            this.view = view;
        }

        @Override
        public void onGenerated(Palette palette) {
            int paletteColor = 0;
            switch (view.getId()) {
                case R.id.vibrant:
                    Palette.Swatch swatch = palette.getVibrantSwatch();
                    paletteColor = palette.getVibrantColor(0x000000);
//                    paletteColor = swatch.getRgb();
                    break;
                case R.id.vibrantLight:
                    paletteColor = palette.getLightVibrantColor(0x000000);
                    break;
                case R.id.vibrantDark:
                    paletteColor = palette.getDarkVibrantColor(0x000000);
                    break;
                case R.id.muted:
                    paletteColor = palette.getMutedColor(0x000000);
                    break;
                case R.id.mutedLight:
                    paletteColor = palette.getLightMutedColor(0x000000);
                    break;
                case R.id.mutedDark:
                    paletteColor = palette.getDarkMutedColor(0x000000);
                    break;


            }
            if (mActionBar != null) {
                mActionBar.setBackgroundDrawable(new ColorDrawable(paletteColor));
            }

        }
    }

    public static Bitmap drawableToBitmap(Drawable drawable) {
        if (drawable instanceof BitmapDrawable) {
            return ((BitmapDrawable) drawable).getBitmap();
        }

        int width = drawable.getIntrinsicWidth();
        width = width > 0 ? width : 1;
        int height = drawable.getIntrinsicHeight();
        height = height > 0 ? height : 1;

        Bitmap bitmap = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(bitmap);
        drawable.setBounds(0, 0, canvas.getWidth(), canvas.getHeight());
        drawable.draw(canvas);

        return bitmap;
    }

}

```

或者
```java
public static int getPaletteColor(Bitmap bitmap) {
        int color = -12417291;
        Palette palette = Palette.from(bitmap).generate();
        Palette.Swatch vibrant = palette.getVibrantSwatch();
        Palette.Swatch vibrantdark = palette.getDarkVibrantSwatch();
        Palette.Swatch vibrantlight = palette.getLightVibrantSwatch();
        Palette.Swatch Muted = palette.getMutedSwatch();
        Palette.Swatch Muteddark = palette.getDarkMutedSwatch();
        Palette.Swatch Mutedlight = palette.getLightMutedSwatch();

        if (vibrant != null) {
            color = vibrant.getRgb();
        } else if (vibrantdark != null) {
            color = vibrantdark.getRgb();
        } else if (vibrantlight != null) {
            color = vibrantlight.getRgb();
        } else if (Muted != null) {
            color = Muted.getRgb();
        } else if (Muteddark != null) {
            color = Muteddark.getRgb();
        } else if (Mutedlight != null) {
            color = Mutedlight.getRgb();
        }
        return color;
    }
```
