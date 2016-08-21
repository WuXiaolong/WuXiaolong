title: markdownGuide
date: 2016-08-18 22:57:51
tags:
category:
---
# 微信公众号
\**Android高手进阶之路**，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。

http://wowubuntu.com/markdown/
http://www.ituring.com.cn/article/504

# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 最后
Markdown语法介绍完毕，通篇都是以我的公众号示例，看样子你有必要关注一下，哈哈哈。


> Markdown是什么，我还真不知道，嘿嘿，Markdown的语法全由一些符号所组成，易读易写。本文主要分享我平时经常使用的Markdown语法（非全部）。

# 引用
## 单引用
使用符号 **>** 标记区块引用，如：
\> 我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
> 我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。

## 引用内的引用
**>** 标记再使用**>>** 标记，如：
\> 我的微信公众号：吴小龙同学
\>> 不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
> 我的微信公众号：吴小龙同学
>> 不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。

# 标题

1、用底线的形式，= （第一阶标题）和 - （第二阶标题），数量不限，如：

只要是截图，左边是语法，右边是效果，下同。
![](http://7q5c2h.com1.z0.glb.clouddn.com/markdowm1.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

2、行首插入 1 到 6 个 # ，对应到标题 1 到 6 阶，如：
![](http://7q5c2h.com1.z0.glb.clouddn.com/markdowm2.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

**我比较喜欢用第二种语法**。

# 链接 
\[吴小龙同學]\(http://wuxiaolong.me/)，效果如下：
[吴小龙同學](http://wuxiaolong.me/)

# 图片
\![我的公众号]\(http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)，前面的文本可不写，效果如下：
![吴小龙同學](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 代码块
\```java
这里有很多代码
\```
java代表代码语言，效果如下：
```java
public class PaintCanvas extends View {
    private Paint mPaint;

    // 省略构造方法
    
    private void init() {       
        mPaint = new Paint();
        mPaint.setAntiAlias(true);        
    }

    @Override
    protected void onDraw(Canvas canvas) {
        canvas.drawCircle(500, 550, 500, mPaint);
    }

}
```
# 横线
比如代码过时，会有一个横线，可用分别用两个**~**包含内容表示，效果如下：
~~代码过时~~

# 列表
##无序列表
使用*、+、—作为列表标记，如：

\* 我的微信公众号：吴小龙同学
\* 我的微信公众号：吴小龙同学
\* 我的微信公众号：吴小龙同学

\+ 我的微信公众号：吴小龙同学
\+ 我的微信公众号：吴小龙同学
\+ 我的微信公众号：吴小龙同学

\- 我的微信公众号：吴小龙同学
\- 我的微信公众号：吴小龙同学
\- 我的微信公众号：吴小龙同学

三种写法都是如下效果：
* 我的微信公众号：吴小龙同学
* 我的微信公众号：吴小龙同学
* 我的微信公众号：吴小龙同学

## 有序列表
只要数字接着一个英文句点，如：
![](http://7q5c2h.com1.z0.glb.clouddn.com/markdowm3.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)



# 分隔线
三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西

* * *

***

*****

- - -

---------------------------------------


# 最后
Markdown语法介绍完毕，通篇都是以我的公众号示例，看样子你有必要关注一下，哈哈哈。






















