---
title: 关于Android命名规范
date: 2016-10-08 20:27:54
tags: Android
category: Android
---

> 第一家公司是如此的重要，如果开发流程规范，对你之后的影响不是一般的大！而我经历的公司大都不成体系，我的习惯就是我的规范！哈哈！

<!--more-->

很多的技术人员，恐怕都认为除了自己的作品外，别人写的代码都是“垃圾”。接手别人的项目，如果对方代码规范，是好事，可以学学人家如何写的，如果代码随意，那就蛋疼了，像我这种强迫症患者，一刻都不能忍，分分钟就想重构！

下面列举几个我接盘项目遗留的坑：

# 包名
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidNameSample1.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
包名全部小写编写，连续的单词只是简单地连接起来，不使用下划线。

# 类名
## 大小写
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidNameSample2.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
类名都以UpperCamelCase风格编写，大驼峰式命名法，即每个单词都是首字母大写。

## 中英文混用
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidNameSample3.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
如果都是拼音或英文命名，无可厚非，但是混用，显得不伦不类，这里墙裂建议都是英语命名，不会的单词有谷歌。

# 变量
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidNameSample4.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
变量名只能是字母（a-z A-Z），下划线（_），或者美元符号($)开始，数字不能放在变量名首位，通常第一个单词的首字母小写，其他单词首字母大写。

# 方法名
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidNameSample6.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
方法名都以lowerCamelCase风格编写，小驼峰式命名法，即首字母小写，其他单词都是首字母大写。

# 最后
这些都是最简单不过，稍微注意一下就可以了，习惯的养成真的很重要，毕竟开发不是仅你一个人，代码规范还是必要的，你能受不得别人说你的写的代码是垃圾吗？给别人留下好项目就等于给自己留下好项目，万一哪天烂项目又辗转到你手上呢！



