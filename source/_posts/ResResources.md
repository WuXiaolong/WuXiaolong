title: android获取 res 中的资源
date: 2015-04-30 
categories: [Android]
tags: [Android,Canvas]
---

### 如何获取 res 中的资源

数据包package：android.content.res

### 主要类：Resources

其主要接口按照功能，划分为以下三部分：

getXXXX()

<!-- more -->

例如：

int getColor(int id)

Drawable getDrawable(int id)

String getString(int id)  直接获取res中存放的资源

InputStream openRawResource(int id)  获取资源的数据流，读取资源数据

void parseBundleExtras(XmlResourceParser parser, Bundle outBundle)  从XML文件中获取数据

Resource为每种资源提供了相应的接口来获取这种资源，除了可以直接获取资源外，还额外提供了以数据流的方式获取资源，这在以后的应用程序开发中会经常使用，那么如何获取Resources了，如下：Resources r = this.getContext().getResources();

InputStream in = getResources().getAssets().open("wangfei.jpg");//Assets下文件
