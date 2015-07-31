title: Eclipse中整合git
date: 2014-08-26 
categories: [Life]
tags: [Life,git]
---
首先在github上面创建工程。

在个人主页上点击Responsitories选项卡的New


![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-1.png)

填写一些信息，点Create respository

<!-- more -->

![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-2.png)

github上的项目就创建好了。
在Eclipse中创建一个工程。
然后在工程上右击->Team->Share project->git->next.

![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-3.png)


![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-4.png)

选择Use or create repository in parent folder of project->Create Repository，如下图，点finish。


![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-5.png)

执行完成之后项目文件夹下就会创建好一个.git的文件夹，仓库就创建好了。

由于项目中的有一些文件没有必要上传，比如bin文件夹，需要在项目中设置好。

展开对应的项目，有小箭头的表示需要上传到服务器的目录，在不想要上传的目录上右击，Team->Ignoe,该目录就会被忽略了。

下面来commit代码到本机一下。

项目上右击Team->commit。



![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-6.png)

选中要commit的文件，点commit，完成之后，代码就上传到本地的服务器了。

接下来把代码上传到github的服务器。

命令行中进入到项目的文件夹，运行
git remote add origin https://github.com/WuXiaolong/ApkTest.git



![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-7.png)
再执行
git push -u origin master

成功执行后就像这样：

![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-8.png)
查看github的项目主页，发现文件已经上传好。



![](http://wuxiaolong.qiniudn.com/2014-08-26-eclipse-git-9.png)

之后在Eclipse中修改好项目代码之后，commit，之后pull，为止代码冲突，然后项目上右击Team->push to Upstream就可以进行代码提交。


上传到github上之后，团队中另外的成员可以通过Eclipse->File->Import->Project from Git->URI来提取工程。

在团队开发中，通常在新的一天的开始工作之前，把最新的代码fecth下来。

直接在项目想右击->Team->fetch
再执行->Team->Merge.

服务器上的新文件就会添加进来了。
