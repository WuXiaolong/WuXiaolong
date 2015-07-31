title: Android之ListView属性
categories: [Android]
tags: [Android,ListView]
date: 2013-07-19
---

```java 
	<ListView 
	android:divider="@drawable/song_item_line"  item之间的分割线
        android:cacheColorHint="#00000000"拖动系统切换item时的缓存色
        android:scrollbars="none"不显示滚动条
        android:listSelector="#00000000"/>选中时背景色
        android:fadingEdge="none" 设置后上边和下边没有阴影了
        android:fastScrollEnabled="true" 快速滚动滑块
        android:cacheColorHint 如果你是用图片做背景的话，那也只要将android:cacheColorHint指定为透明（#00000000）就可以了.  
        android:divider="@drawable/list_driver" 设置显示分割线图形，如果不想显示分割线则只要设置为
        android:divider="@null" 就可以了，分割线可以自定义颜色、或图片.
        android:dividerHeight="6px"设置分割线高度像素.  
        android:fadeScrollbars="true"  滚动条的自动隐藏和显示. 
        android:transcriptMode="alwaysScroll"  （支持ScrollBar）自动滑动到最底部.
        android:stackFromBottom="true" 设置你做好的列表显示在列表的最下面，值为true和false. 
        android:drawSelectorOnTop="true"  点击某一条记录，颜色会显示在最上面，记录上的文字被遮住，所以点击文字不放，文字就看不到
        android:drawSelectorOnTop="false" 点击某条记录不放，颜色会在记录的后面，成为背景色，但是记录内容的文字是可见的
	/>
```


