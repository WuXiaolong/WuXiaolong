title: github之Picasso使用
date: 2015-09-14 11:32:12
tags: Picasso
category: Library
---
>github地址：[https://github.com/square/picasso](https://github.com/square/picasso)

## Gradle:
```java
compile 'com.squareup.picasso:picasso:2.5.2'
```
## 加载网络图片
```java
Picasso.with(context).load("http://i.imgur.com/DvpvklR.png").into(imageView);
```
<!--more-->
## 加载图片，设置回调
```java
Picasso.with(context).load(imageUrl)
                    .into(imageView, new Callback() {
                        @Override
                        public void onSuccess() {
                           
                        }

                        @Override
                        public void onError() {
                            
                        }
                    });
```
## 设置图片尺寸
```java
Picasso.with(context)
  .load(url)
  .resize(50, 50)
  .centerCrop()
  .into(imageView)
```

## 设置预加载和错误图片
```java
Picasso.with(context)
    .load(url)
    .placeholder(R.drawable.user_placeholder)
    .error(R.drawable.user_placeholder_error)
    .into(imageView);
```
## 加载本地图片
```java
Picasso.with(context).load(R.drawable.landing_screen).into(imageView1);
Picasso.with(context).load("file:///android_asset/DvpvklR.png").into(imageView2);
Picasso.with(context).load(new File(...)).into(imageView3);
```