---
title: 手把手教你做个人 app
date: 2016-11-11 22:54:16
tags: PersonalApp
category: App
---

我们都知道，开发一个app很大程度依赖服务端：服务端提供接口数据，然后我们展示；另外，开发一个app，还需要美工协助切图。没了接口，没了美工，app似乎只能做成单机版或工具类app，真的是这样的吗？先来展示下我的个人app，没有服务端，没有美工完成的，换言之，我干了所有人的活：
<!--more-->
![](http://7q5c2h.com1.z0.glb.clouddn.com/PersonalApp4.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
这个app叫“微言”，他对于我意义很重大，最初微言只是我一个练手的项目，刚刚工作，技术有限，微言只是sqlite记事本类app，只能本地操作，后来慢慢演变现在几近完美的app，从中我学到很多，熟悉了立项到上线的整个流程，最新技术得以实践，从一个程序猿思维从而向产品思维转变，简单的Photoshop等。当然长期的积累自然会带来经济方面的收益，这里秀下我的app广告收益，我的所有app之和：
![](http://7q5c2h.com1.z0.glb.clouddn.com/PersonalApp1.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
最多一个月4000多，4000多什么概念，比我当时薪资都高呢，这些“成就”有了我可以在此吹牛的资本，哈哈哈！

接下来，我一一分析，带您完成这样的一个完整的app。

# 没有服务端
## jsoup
我无意听到大牛同事说到解析html，比较有兴趣去搜索这是什么玩意儿，知道了一个强大的东西jsoup，jsoup能解析html，即网站，于是我的微言脱离了单机版。对用户而言，他不在乎数据从何而来，管您是从接口取的还是解析html，他们关心的是app体验和功能的完善。我就这样瞒天过海，数据取之网页了，群里之前太多人太多人问我用的什么服务器，回复太多次解析html后就不愿意再回复了。

我选择这种方式有个最大的好处就是数据不需要本人维护，巧妙地避开了我不会服务端开发，更不需要做接口；解析html也有个最大的弊端，一旦对方网站节点变化了，或许您的app就挂了，必须及时去更新。

### 使用方法
**步骤一**：
首先网络请求，这里用的Retrofit，具体见：[Android MVP+Retrofit+RxJava实践小结](http://wuxiaolong.me/2016/06/12/mvpRetrofitRxjava/)。以解析我的博客http://wuxiaolong.me/ 示例，可以拿到类似以下数据：
![](http://7q5c2h.com1.z0.glb.clouddn.com/PersonalApp2.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
在谷歌浏览器，我的博客页面点击右键-查看网页源代码（V），同样看到以上数据。

**步骤二**：
1、app/build.gradle
```java
compile 'org.jsoup:jsoup:1.10.1'
```
2、解析html
要诀：多观察html节点、标签。
先观察我们要解析的数据（以我的博客http://wuxiaolong.me/ 示例），首页分别有标题、发表时间、文章分类、文章评论、文章摘要5个元素谷歌浏览器，我们这次只需要标题、发表时间、文章摘要；可以看到我的博客是分页，第一页网址是http://wuxiaolong.me 和第二页网址却是http://wuxiaolong.me/page/2/ ，之后区别就是页码，因此app做分页的话要判断第一页和其他页，最终我做成的效果：
![](http://7q5c2h.com1.z0.glb.clouddn.com/PersonalApp3.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
我们一一分析，关于jsoup语法，这里不说，见 [jsoup官网](https://jsoup.org/)。

（1）标题数据结构如下：
```xml
<h1 class="post-title" itemprop="name headline">
<a class="post-title-link" href="/2016/10/31/AppShortcuts/" itemprop="url">Android App Shortcuts</a>
</h1>
```
观察可根据class="post-title"的getElementsByClass解析：
```java
//responseBody是retrofit网络请求返回的，转成String，即我们需要解析的数据
Document document = Jsoup.parse(new String(responseBody.bytes(), "UTF-8"));
List<Element> titleElementList = new ArrayList<>();
Elements titleElements = document.getElementsByClass("post-title-link");
for (Element element : titleElements) {
    titleElementList.add(element);
    //text拿到文本，如这里的“Android App Shortcuts”
    LogUtil.d("text=" + element.text());
    //拿到href属性值，如这里“/2016/10/31/AppShortcuts/”，即博客链接，如果跳转详情需要加上“http://wuxiaolong.me”
    LogUtil.d("href=" + element.attr("href"));
}
```

（2）发表时间数据结构如下：

```xml
<span class="post-time">
<span class="post-meta-item-icon">
<i class="fa fa-calendar-o"></i>
</span>
<span class="post-meta-item-text">发表于</span>
<time itemprop="dateCreated" datetime="2016-10-31T21:49:53+08:00" content="2016-10-31">2016-10-31</time>
</span>
```
观察，同样用getElementsByClass解析：
```java
List<Element> timeElementList = new ArrayList<>();
Elements timeElements = document.getElementsByClass("post-time");
for (Element element : timeElements) {
    //这里通过解析"time"标签，然后取文本，即“2016-10-31”
    LogUtil.d("time=" + element.getElementsByTag("time").text());
    timeElementList.add(element);
}
```
（3）文章摘要数据结构如下：
```xml
<div class="post-body" itemprop="articleBody">
<h1 id="简介"><a href="#简介" class="headerlink" title="简介"></a>简介</h1>
<p>Android 7.1允许您定义应用程序中特定操作的快捷方式。这些快捷键可以显示桌面，例如Nexus和Pixel设备。快捷键可让您的用户在应用程序中快速启动常见或推荐的任务。<br>每个快捷键引用一个或多个意图，每个意图在用户选择快捷方式时在应用程序中启动特定操作。可以表达为快捷方式的操作示例包括：<br>
<div class="post-more-link text-center">
<a class="btn" href="/2016/10/31/AppShortcuts/" rel="contents">
阅读全文 &raquo;</a>
</div>
</p>
</div>
```
恩，也是用的getElementsByClass解析：
```java
List<Element> bodyElementList = new ArrayList<>();
Elements bodyElements = document.getElementsByClass("post-body");
for (Element element : bodyElements) {
	//这里用text()只是拿到文本，但我想要直接返回html标签，很好，jsoup有html()方法。
    LogUtil.d("body=" + element.html());
    bodyElementList.add(element);
}
```
3、核心代码
```java
private void loadMyBlog() {
    Call<ResponseBody> call;
    //分页处理
    if (page == 1) {
        call = apiStores.loadMyBlog();
    } else {
        call = apiStores.loadMyBlog(page);
    }
    call.enqueue(new RetrofitCallback<ResponseBody>() {
        @Override
        public void onSuccess(ResponseBody responseBody) {
            try {
                Document document = Jsoup.parse(new String(responseBody.bytes(), "UTF-8"));
                List<Element> titleElementList = new ArrayList<>();
                Elements titleElements = document.getElementsByClass("post-title-link");
                for (Element element : titleElements) {
                    titleElementList.add(element);
                    //LogUtil.d("text=" + element.text());
                    //LogUtil.d("href=" + element.attr("href"));
                }
                List<Element> timeElementList = new ArrayList<>();
                Elements timeElements = document.getElementsByClass("post-time");
                for (Element element : timeElements) {
                    //LogUtil.d("time=" + element.getElementsByTag("time").text());
                    timeElementList.add(element);
                }
                //Elements categoryElements = document.getElementsByClass("post-category");
                //for (Element element : categoryElements) {
                //    LogUtil.d("category=" + element.getElementsByTag("a").text());
                //}
                List<Element> bodyElementList = new ArrayList<>();
                Elements bodyElements = document.getElementsByClass("post-body");
                for (Element element : bodyElements) {
                    LogUtil.d("body=" + element.html());
                    bodyElementList.add(element);
                }
                if (page == 1) {
                    dataAdapter.clear();
                }
                dataAdapter.addAll(titleElementList, timeElementList, bodyElementList);
                if (titleElementList.size() < 8) {
                    //因为我的博客一页8条数据，当小于8时，说明没有下一页了
                    pullLoadMoreRecyclerView.setHasMore(false);
                } else {
                    pullLoadMoreRecyclerView.setHasMore(true);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        @Override
        public void onFailure(int code, String msg) {
            toastShow(msg);
        }
        @Override
        public void onThrowable(Throwable t) {
            toastShow(t.getMessage());
        }
        @Override
        public void onFinish() {
            pullLoadMoreRecyclerView.setPullLoadMoreCompleted();
        }
    });
    addCalls(call);
}
```
### jsoup解析源码
解析我的博客源码已经上传我的github，见：https://github.com/WuXiaolong/WeWin 

想必这样一一分析，您一定会jsoup解析html，如果还不会，私下给我发个大红包，我再手把手教您，发个超大红包，今晚我就是您的啦，嘿嘿。

### 题外
可能您担心，jsoup解析html，这样爬虫难道不侵权吗？是的，我也担心，所以我的app也只在我的群里“宣传宣传”。

## bmob
仔细的您，肯定发现了，jsoup爬数据，只能做展示功能，那我的微言里不是有评论功能嘛！这是怎么做到的呢？我刚开始做app那会，个人app是做不了POST功能的，但bmob出现解决了个人开发者这个没有服务器的痛点，它就相当于一个数据库，提供了sdk，您可以做增删改查操作，我们只需要专注客户端，后端就不用管了，话说如此，我们还是稍微懂点数据库知识的，以便于我们更好利用bmob。除了bmob，现在还有leancloud、apicloud等都有类似的服务，很感谢他们，及时解决我们的刚需，个人app还可以有一线生机。

关于bmob、leancloud、apicloud如何使用，我知道聪明的您已经在看他们的官方文档了。

# 没有美工
## 美工切图
在实际开发中，有些效果，只需要美工做张图片就能轻松搞定，没有美工切图的配合，app开发似乎难以进展下去了，是吗？其实我在《[Android Design Support Library使用](http://wuxiaolong.me/2015/11/06/DesignSupportLibrary/)》一文提到一句话：“目前这个sample，Material design风格的效果都有了，相当一个空壳子，您只需在实际开发中塞真实数据就是一个perfect app”，对，就用谷歌的Material design风格就OK了，它提供了一套规范、图片，足够我们个人app使用了，而且现在还有vector，更是强大之极。比如微言-每日推荐右上角的刷新按钮，如图：
![](http://7q5c2h.com1.z0.glb.clouddn.com/PersonalApp5.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
相应的xml：
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/action_refresh"
        android:icon="@drawable/ic_loop_24dp"
        android:title="刷新"
        app:showAsAction="always" />
</menu>
```
平时ic_loop_24dp肯定是张图片，然而我用的vector，代码如下：
```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:width="24dp"
        android:height="24dp"
        android:viewportWidth="24.0"
        android:viewportHeight="24.0">
    <path
        android:fillColor="?attr/colorControlNormal"
        android:pathData="M12,4L12,1L8,5l4,4L12,6c3.31,0 6,2.69 6,6 0,1.01 -0.25,1.97 -0.7,2.8l1.46,1.46C19.54,15.03 20,13.57 20,12c0,-4.42 -3.58,-8 -8,-8zM12,18c-3.31,0 -6,-2.69 -6,-6 0,-1.01 0.25,-1.97 0.7,-2.8L5.24,7.74C4.46,8.97 4,10.43 4,12c0,4.42 3.58,8 8,8v3l4,-4 -4,-4v3z"/>
</vector>
```
是不是很神奇，vector如何创建：
![](http://7q5c2h.com1.z0.glb.clouddn.com/AppShortcuts6.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
这里随便演示，创建了一个vector，关于vector学习可参考医生的《[Android Vector曲折的兼容之路](http://blog.csdn.net/eclipsexys/article/details/51838119)》一文，写的很详细，这里不多说了。

## app图标
app当然希望有个漂亮的有意义的图标，会用到Photoshop，我当然不会啊，那必须得学啊。一般安卓市场需要图标尺寸16*16，48*48，512*512，圆角，Android开发需要48*48，72*72，96*96，144*144，196*196，因此PS的时候，只需要做个最大尺寸512*512，然后缩小即可。
图标的PS步骤：
1、用photoshop打开您想修改的图片
2、在左侧工具栏中选择“圆角矩形工具”（默认的是“矩形工具”，您只需要右击图标就可以发现“圆角矩形工具”），如上图
3、在上面“半径”框中输入您想要的圆角半径，一般图片选25即可，为了效果明显我设置为40，看上图有一个框显示半径40.
4、在打开的图片上画一个圆角矩形，把图片覆盖住。
5、对着已经被覆盖的图片选区右击，选择“建立选区”，如果有窗口弹出直接点击“确定”，在弹出的选项中直接点击“确认”
6、在上方的“选择”选项卡中点击，在下拉框中找到“反向”，也可以使用快捷键ctrl+shilf+i。
7、在右下方的图层栏中双击“背景”图片（上面第一张图片右下角可以看到），如果有窗口弹出直接点击“确定”，完成解锁。
8、按键盘上的"DELETE"键清除四个直角。
9、继续右击“形状1”（在画面右下方图层那里可以找到），在弹出选项中选择“删除图层”，如果有窗口弹出直接点击“是”。
10、OK，您可以看到一个圆角图片。
11、最后在左上角点击文件--》存储为--》选择png格式（其他格式也可以），完成。
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyan512.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
为什么微言的图标是一个“言”字，因为我觉得这样简洁大方，简单明了，言简意赅……算了，不装了，其他我不会P啊！

# 推广技巧
做完一个个人app，是不是很有成就感啊，上线安卓市场，却没几个下载量，尼玛，劳资花了很长时间呢，这么牛B的app怎么没人下载？！心情一下子跌倒谷底，那得让更多的人知道自己的app啊，我是这样做的：

1、邀请好评
您去下载一个app时，可能会看下这个app的评论，如果有很多好评，您会不会更愿意去下载呢，是的，来看我的微言好评如潮：
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyan_good_comment.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
哈哈哈，是不是很牛，邀请评论我写成了工具类了，请笑纳：
```java
public class InviteCommentUtil {
    private String mDateFormat = "yyyy-MM-dd";
    private String mInviteCommentTime;

    /**
     * 选择哪天弹邀请评论框
     */
    public void startComment(final Activity activity) {
        mInviteCommentTime = PreferenceUtils.getPreferenceString(activity, "inviteCommentTime", TimeUtil.getCurrentTime(mDateFormat));
        String saveCommentTime = PreferenceUtils.getPreferenceString(activity, "saveCommentTime", TimeUtil.getCurrentTime(mDateFormat));
          int compareDateValue = TimeUtil.compareDate(mInviteCommentTime, saveCommentTime, mDateFormat);
        if (compareDateValue == 1) {
            AlertDialog.Builder builder = new AlertDialog.Builder(
                    activity);
            int nowReadingTotal = ReadUtil.getReadedTotal();
            builder.setMessage(Html.fromHtml("您已经累计阅读<font color=#FF0000>" + nowReadingTotal + "</font>字，再接再厉哦！如果喜欢我，希望您能在应用市场给予<font color=#FF0000>五星</font>好评！"));
            builder.setTitle("求好评");
            builder.setPositiveButton("好评鼓励",
                    new android.content.DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            setComment(activity);
                            try {
                                Intent intent = new Intent(
                                        "android.intent.action.VIEW");
                                intent.setData(Uri
                                        .parse("market://details?id=com.android.xiaomolongstudio.weiyan"));
                                activity.startActivity(intent);
                                activity.overridePendingTransition(
                                        R.anim.enter_right_to_left, R.anim.exit);
                            } catch (Exception e) {
                                e.printStackTrace();
                            }

                            dialog.dismiss();
                        }
                    });
            builder.setNegativeButton("下次再说",
                    new android.content.DialogInterface.OnClickListener() {

                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            setComment(activity);
                            dialog.dismiss();
                        }
                    });
            builder.create().show();
        }
    }

    /**
     * 保存，直到下周再提示
     */
    private void setComment(Activity activity) {
        PreferenceUtils.setPreferenceString(activity, "saveCommentTime", mInviteCommentTime);
    }
}
```
注意：这里邀请评论时间控制好，不能太频繁了，不然用户会反感的。

2、专注一个市场
不知道您有没有发现，某个市场您明明没有发布，却能搜到您的app，没错，一些市场会抓您的这个app，比如豌豆荚，百度，因此策略，专注一个市场，这个市场出名了，还怕其他市场不知道吗？当然我们是争取每个市场都能发布上线，多一个下载是一个。
微言位于分类下前排
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyan_rank.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
多次进入精品系列
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyan_rank2.png?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

如何进入前排或精品，邀请好评是关键的一步。

3、新品推荐
新品上线，很多市场有新品推荐，比如小米、妹纸、360、应用宝，一般需要自荐，一旦被推荐，下载量是可观的，微言肯定被推荐过啦。哦，更新版本也算新品哦。

4、微博
这是我好哥们教我的，他真的好牛，个人app做的更牛，给您们看一个链接：http://weibo.com/p/1008082a702d2cb6146485e5dc3d624d31def6 ，就知道如何在微博上推广了，没错，就是话题，用两个#号圈起来，发微博，就是一个话题，别人可以这个话题下讨论，无形中形成了推广作用。

5、app分享
app分享功能肯定是要做的，万一用户觉得您的这个app很棒，想推荐给好友，结果没分享功能，岂不歇菜，分享微博可以加两个#号圈起来哦。

6、QQ群
如果您直接群里发app的下载链接，只会一个结果：被T。像我们程序猿，大部分都是技术群，我的做法是写文章分享我app用的技术，文章会附上app下载地址，然后有这个技术兴趣的人可能询问，这样我就名正言顺地“推广”了，哈哈，我好坏！

以上仅我知道的，不一定有效，毕竟我不是专业的推广人员。

# 如何赚钱
万事俱备，只欠东风，当您的用户量足够大的时候，自然有人找您，投资您的app，这个过程前期0收益，耗得精力时间不算，可能还得烧钱，不适合个人开发者，我选择的是赚赚小钱，app加广告的方式。
广告平台选择第三方的，最早我使用的多盟，但是一段时间发现我根本没什么收益，感觉多盟扣量好严重，之后尝试多易传媒、艾德思奇、芒果聚合，也使用过点乐积分，发现一些市场不让上线积分类app，放弃之，还有百度移动广告联盟、腾讯的广点通，谷歌的也玩过，收益最稳定属百度的，这点不黑它。
至于广告集成，也是提供sdk，自行去他们的官网了解吧。


# 推荐阅读
[一套完整的Android通用框架](http://mp.weixin.qq.com/s?__biz=MzI4MTQyNDg3Mg==&mid=2247483753&idx=1&sn=9a6a2052bfc4c58883b72bc02ba97210&chksm=eba827eedcdfaef805812de7e37ed7ddc6fb52244bd3f5b4bca26c225e5e91a42d3862f8c5ce#rd)
[Android Design Support Library使用](http://mp.weixin.qq.com/s?__biz=MzI4MTQyNDg3Mg==&mid=2247483752&idx=1&sn=e5a12a53d12dbe80c27ae78af9660b12&chksm=eba827efdcdfaef999ac120869fb3b32508d76f883d1c9b2701c840834a9feef53d0ed9669a3#rd)
[AndroidMVPSample](http://mp.weixin.qq.com/s?__biz=MzI4MTQyNDg3Mg==&mid=2247483703&idx=1&sn=0bc01a24c29ec31ffed98838ee9ad2a0&chksm=eba827b0dcdfaea6b95c6f54b7aa3a905b723180ca6feae08c211345327e8b7932efc732ded1#rd)

# 联系作者
我的微信公众号：吴小龙同学，欢迎关注交流~

![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 最后
我的几个app，都是我个人做到了很满意，功能都很完善，以至于我后来觉得没什么好更新的，再加上广告收益好也就一段时间，移动广告商雨后春笋，导致广告单价太低，而且安卓市场对个人开发者各种限制，比如不让上线视频类app；某度，某60必须用自家的加固才让上线app等，就没什么动力继续维护app，做事还是要有动力的，不然活着干吗？不过我知道有人还在坚持做个人app，做的好的，日入几百甚至上千的。app最终目的就是赚钱。



