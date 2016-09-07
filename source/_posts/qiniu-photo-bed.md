title: 使用七牛作为github博客的图床
date: 2014-10-30 
categories: hexo
tags: [github]
---

文章的图片存放:

1.墙裂推荐七牛云储存,[注册地址](https://portal.qiniu.com/signup?code=3lk4saqepkdhu)。

2.七牛云储存提供10G的免费空间,以及每月10G的流量.存放个人博客图片最好不过了

3.七牛云储存还有各种图形处理功能、缩略图、视频存放速度也给力（非打广告）。

<!-- more -->

首先要有个七牛账号，然后建立一个空间，我这里随便起名为WuXiaolong。空间建立好以后，进入空间设置界面：
![1](http://wuxiaolong.qiniudn.com/2014-10-30-qiniu-photo-bed-1.png)
进入`基本设置`->`镜像存储`，写上你要镜像加速的域名，这里是我的github路径：
![2](http://wuxiaolong.qiniudn.com/2014-10-30-qiniu-photo-bed-2.png)
点击确定就ok了。

然后在写博客的时候，注意图片的域名不要写相对路径了，要写类似于
![3](http://wuxiaolong.qiniudn.com/2014-10-30-qiniu-photo-bed-3.png)
这样的指向七牛镜像服务的url，其中/images开始的url和之前使用的相对路径一致。

七牛镜像加速基本原理是当有指向七牛的图片请求时，七牛会先看自己的空间下是否有该图片，如果不存在，则去上面设置的原始地址去取，同时在七牛下面保留一份，后续再有同样的请求时则直接由七牛返数据。








