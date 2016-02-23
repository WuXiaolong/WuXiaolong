title: Android基于XMPP开发（一）【openfire服务器配置】
date: 2016-02-24 18:58:28
tags: [XMPP,Smack,openfire]
category: XMPP
---
# OpenFire 
OpenFire 是采用Java开发的基于XMPP（Jabber）协议，开源实时协作（RTC）服务器。
Smack 是用 Java编 写的XMPP客户端代码库，是 spark 的核心
开源界总是有许多有趣的东东，这三个合起来就是一个完整的XMPP IM 实现。
OpenFire ——服务器端
Spark——客户端
Smack——XMPP 传输协议的实现
<!-- more -->
三者都是基于Java 语言的实现，其中XMPP是一个协议，协议是需要实现的，Smack起到的就是这样的一个作用。
OpenFire 是基于XMPP 协议的IM 的服务器端的实现，虽然当两个用户连接后，可以通过点对点的方式来发送消息，但是用户还是需要连接到服务器来获取一些连接信息和通信信息的，所以服务器端是必须要实现的。
Spark 提供了客户端一个基本的实现，并提出了一个很好的插件架构，这对于开发者来说不能不说是一个福音。我强烈建议基于插件方式来实现你新增加的功能，而不是去改它的源代码，这样有利于你项目架构，把原始项目的影响降到最低。
Smack 是一个XMPP 协议的Java 实现，提供一套可扩展的API，不过有些时候，你还是不得不使用自己定制发送的XML 文件内容的方式来实现自己的功能。

 三者之间的关系：
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-0.png)
从上图可知，Client 端和Server端都可以通过插件的方式来进行扩展，Smack是二者传递数据的媒介。
以上介绍来源网络。

# 配置本地服务器
## openfire下载
[点击下载](http://www.igniterealtime.org/downloads/index.jsp)

## 配置

* 1、选择中文，continue
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-1.png)

* 2、默认继续
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-2.png)

* 3、默认继续
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-3.png)

* 4、本教程数据库MySQL，此步之前先建数据库名为openfire，数据库驱动选项选择MySQL，OK。这里数据库URL替换如图所示，继续
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-4.png)

* 5、默认继续
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-5.png)

* 6、填写密码，用户名admin，不然密码默认admin
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-6.png)

* 7、登录后台即可
![](http://wuxiaolong.qiniudn.com/2015-01-14-xchat-7.png)

这样openfire配置到此完成。

# XMPP公共服务器
这是公共XMPP服务器列表，免费供大家使用。
[Public XMPP servers](https://list.jabber.at/)
[Jabber.org](http://www.jabber.org/)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 附录
[Openfire与XMPP协议](http://www.cnblogs.com/hoojo/archive/2012/06/18/2553975.html)
[XMPP-RFC3920中文](http://wenku.baidu.com/link?url=OSVHeZETSwiNRJArOxcgU4Ek1MGCu83TPh5aArKtDiIUz6sisSOtpNn4sI9RYTywGkhgJX3pkGpqeJQmeLMf_YdPX3K2VegoxDk0sHwxmCy)