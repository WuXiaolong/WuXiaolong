title: Android library分享到jcenter
date: 2016-05-06 11:07:29
tags: jcenter
category: jcenter
---
> 之前写过同样的一篇教程（地址：[Android library分享到jcenter](http://wuxiaolong.me/2015/11/12/jcenter/)），但是配置较多，还不能自己写库名（最新的貌似可以写），起码当时不能（直接取的建库时的名），这点被坑了，后来想改名都不行，后文会说。这篇教程和之前差不多，就配置这块不一样。好，快上车，老司机要开车了。

# 申请bintray账号
登录 [bintray.com](https://bintray.com/)
在[bintray.com](https://bintray.com/)上注册一个账号。
点击maven，点击Add New Package，为我们的library创建一个新的package。
命名所有字母应该为小写，单词之间用－分割。
需要API Key，点击进入你的账户信息里再点击Edit即有查看API Key的选项，把他复制下来。

# 配置
library项目最好分成两个module，一个Application Module，一个Library Module。Application Module用于展示库的用法，Library Module是library的源代码。
<!--more-->
## 配置项目
修改项目的build.gradle件中的依赖部分，
```java
dependencies {
        classpath 'com.android.tools.build:gradle:2.1.0'
        classpath 'com.novoda:bintray-release:0.3.4'
    }
```

## 配置library
具体配置可参考我的开源库[PullLoadMoreRecyclerView](https://github.com/WuXiaolong/PullLoadMoreRecyclerView/blob/master/library/build.gradle)。
```java
apply plugin: 'com.android.library'
android {
   ……
}

dependencies {
  ……
}
//添加
apply plugin: 'com.novoda.bintray-release'
publish {
    //bintray.com用户名
    userOrg = 'wuxiaolong'
    //jcenter上的路径
    groupId = 'com.wuxiaolong.pullloadmorerecyclerview'
    //项目名称
    artifactId = 'pullloadmorerecyclerview'
    //版本号
    publishVersion = '1.0.5'
    //描述，不重要
    desc = '实现RecyclerView下拉刷新和上拉加载更多以及RecyclerView线性、网格、瀑布流效果演示'
    //网站，不重要
    website = 'https://github.com/WuXiaolong/PullLoadMoreRecyclerView'

}
```
`是不是配置少了很多哇，这里解释下，本来我想把项目名称改成pullloadmorerecyclerview，上传也成功了，但是依赖却找不到了，之前默认名是library，我改成了library，上传，这样算一个新的库，得重新审核，哎无奈，只能按照之前的方法去上传。`

# 上传library到bintray空间
请到Android Studio的终端（Terminal）选项卡。
输入下面的命令：

```js
gradlew clean build bintrayUpload 
 -PbintrayUser=wuxiaolong 
 -PbintrayKey=这里填写刚刚保存的API Key
 -PdryRun=false
```
如果显示如下，说明成功了！否则有log提示错误。
```js
BUILD SUCCESSFUL
```
![](http://7q5c2h.com1.z0.glb.clouddn.com/includeMyPackage.png)
上传完成即可在Bintray网站上找到你的Repo，我们需要完成最后一步工作，申请你的Repo添加到JCenter。可以进入[这个页面](https://bintray.com/bintray/jcenter)，点击Include My Package，输入你的项目名字，点击匹配到的项目，直接send即可，然后就等管理员批准了。
![](http://7q5c2h.com1.z0.glb.clouddn.com/jcenterApproved.png)
如上，网站上会给你一条通过信息，然后就OK了，大功告成。

```java
compile 'com.wuxiaolong.pullloadmorerecyclerview:library:1.0.5'
```
一般来说，我们需要知道library的字符串形式，包含3部分
GROUP_ID:ARTIFACT_ID:VERSION
上面的例子中，GROUP_ID是com.wuxiaolong.pullloadmorerecyclerview ，即配置library中group的值；ARTIFACT_ID是library，配置library中没有写到，是library项目的名字；VERSION是1.0.5。
查看bintray上库：
http://jcenter.bintray.com/com/wuxiaolong/

# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 附录
[bintray-release](https://github.com/novoda/bintray-release)
[Android 快速发布开源项目到jcenter](http://blog.csdn.net/lmj623565791/article/details/51148825)
