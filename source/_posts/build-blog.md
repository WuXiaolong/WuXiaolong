title: 手把手教你建github技术博客by jekyll
date: 2014-10-26 07:56:29 
categories: jekyll
tags: [github,jekyll]
---


******

## <a name="index"/>目录
* [一、适合人群](#for-the-crowd)
* [二、github创建博客](#build-blog)
	* 注册账号
	* 创建页面仓库
	* 项目设置
	* 页面生成器
	* 创建用户页面
	* 页面发布
	* 查看效果
* [三、如何发布博客](#write-blog)
	* 安装 Ruby
	* 安装 DevKit
	* 安装 Python
	* 安装 Jekyll
	* 启动 Jekyll
	* 图床
* [四、总结](#ps)
* [五、附录](#appendix)

<!-- more -->

<a name="for-the-crowd"/>

## 一、适合人群

* 喜欢写Blog的人
* 有一定的编程基础
* 爱折腾的人
* 熟练使用版本控制Git
* 了解使用Github
* 熟悉基本的MarkDown语法



<a name="build-blog"/>

## 二、github 创建博客

* ### 注册账号
地址：[https://github.com/](https://github.com/)  
输入账号、邮箱、密码,然后点击注册按钮.
![1](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-1.png)

* ### 创建页面仓库
这个仓库的名字需要和你的账号对应，格式:`yourname`.github.io
输入基本信息，然后点击创建仓库.
![2](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-2.png)
![3](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-3.png)

* ### 项目设置
![4](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-4.png)

* ### 页面生成器
![5](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-5.png)

* ### 创建用户页面
![6](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-6.png)

* ### 页面发布
![7](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-7.png)

* ### 查看效果
需要等待十分钟，自此，完成了github的博客创建，访问地址：yourname.github.io
![8](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-8.png)
<a name="write-blog"/>

## 三、如何发布博客

以上只是创建了博客，那怎么发布自己的博客？共分为以下几个重要步骤（window下）：

* 安装 Ruby
* 安装 DevKit
* 安装 Python
* 安装 Jekyll

### 安装 Ruby

* 下载地址：[http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/) 
* 安装
	* 最好保持默认的路径 C:\Ruby200-x64， 因为安装包明确提出 “请不要使用带有空格的文件夹 (如： Program Files)”。
	* 勾选 “Add Ruby executables to your PATH”，这样执行程序会被自动添加至 PATH 而避免不必要的头疼。
	
![9](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-9.png)
	* 命令行ruby -v来检测Ruby是否成功安装	    
	
```js 
	C:\Users\WuXL>ruby -v	
	返回ruby 2.1.3p242 (2014-09-19 revision 47630) [x64-mingw32]，ok
	
```
	
### 五、安装 DevKit

* 下载地址：[http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/) 
* 下载同系统及 Ruby 版本相对应的 DevKit 安装包。 例如，DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe 适用于64位 Windows 系统上的 Ruby 2.0.0 x64。
* 运行安装包并解压缩至某文件夹，如 C:\DevKit
* 通过初始化来创建 config.yml 文件。在命令行窗口内，输入下列命令：

```js
	cd “C:\DevKit”
	ruby dk.rb init
	notepad config.yml
```

* 在打开的记事本窗口中，于末尾添加新的一行 - C:\Ruby200-x64，保存文件并退出。
* 回到命令行窗口内，审查（非必须）并安装。

```js
	ruby dk.rb review
	ruby dk.rb install
```
### 安装 pyhton

* 下载地址：[http://www.python.org/download/](http://www.python.org/download/) 
* 添加安装路径 (如C:\Python27)至PATH。

### 安装 Jekyll


* 查看是否安装 gem 成功

```js
	gem --version
	返回2.2.2，OK
```

* 更新为 淘宝镜像<br>
	RubyGems镜像换为 淘宝、防止下载包出错(官网https://ruby.taobao.org/)

* 移除旧地址
 
```js
	gem sources --remove https://rubygems.org/ 
	返回https://rubygems.org/ removed from sources，ok
```
* 更新为淘宝镜像

```js
	gem sources -a https://ruby.taobao.org/
	返回https://ruby.taobao.org/ added to sources，ok
```
* 安装jekyll

```js
	gem install jekyll 
```
* 查看是否安装jekyll成功

```js
	jekyll -version
	返回jekyll 2.4.0，ok
```
* 安装 依赖包 

```js
	gem install wdm
```
* 安装依赖包代码 高亮 

```js
	gem install rdiscount
```
* 安装 ‘Easy Install’
 
```js
	浏览 https://pypi.python.org/pypi/setuptools#installation-instructions 来查看详细的安装指南。
	对于 Windows 7 的机器，下载 ez_setup.py 并保存，例如，至C:\。 然后从命令行使用 Python 运行此文件：python “C:\ez_setup.py”
	添加 ‘Python Scripts’ 路径 (如： C:\Python27\Scripts) 至 PATH
```
* 安装 Pygments

```js
	确保 easy_install 已经正确安装
	easy_install --version
	输出示例：
	setuptools 3.1
```
* 使用 “easy_install” 来安装 Pygments

```js
	easy_install Pygments
```
* 安装指定版本的yajl


```js
	删除86版本的yajl
	C:\OpenSources\liyouhai>gem uninstall yajl-ruby
	Successfully uninstalled yajl-ruby-1.1.0-x86-mingw32
	安装指定版本的yajl
	C:\OpenSources\liyouhai>gem install yajl-ruby -v 1.1.0 --platform=ruby
	Fetching: yajl-ruby-1.1.0.gem (100%)
	Temporarily enhancing PATH to include DevKit...
	Building native extensions.  This could take a while...
	Successfully installed yajl-ruby-1.1.0
	Parsing documentation for yajl-ruby-1.1.0
	unable to convert "\x90" from ASCII-8BIT to UTF-8 for lib/yajl/yajl.so, skipping
	Installing ri documentation for yajl-ruby-1.1.0
	1 gem installed
```

### 启动 Jekyll

按照官方的 Jekyll[`快速开始手册`](http://jekyllrb.com/docs/quickstart/)的步骤，或者克隆[`模板`](https://github.com/jekyll/jekyll/wiki/Sites)到本地，cmd下cd进入目录运行如下命令，就能在 localhost:4000 中预览了，大功告成。

```js
	jekyll serve -watch (或jekyll serve -w)
```
### 图床

1.墙裂推荐七牛云储存,[注册地址](https://portal.qiniu.com/signup?code=3lk4saqepkdhu)。

2.七牛云储存提供10G的免费空间,以及每月10G的流量.存放个人博客图片最好不过了

3.七牛云储存还有各种图形处理功能、缩略图、视频存放速度也给力（非打广告）。

具体使用见[使用七牛作为github博客的图床](http://wuxiaolong.github.io/blog/2014/10/30/qiniu-photo-bed.html)

<a name="ps"/>

## 四、总结

一开始认为 GitHub.io 技术博客很有难度，从10月23日着手，经历24日一天摸索，知道了jekyll，MarkDown(其实就是github的 READ.md ,幸亏之前自学过READ.md语法），也有幸自学了git，不然还不知道这个建博客 什么时候能做好，进jekyll安装就花了半天，反正各种错误，然后各种谷歌百度  ，25日一天就写了这个博客（集众建博客的帖子所长），问题主要是没有 文章摘要 ，一下子全部显示出来，我有强迫症，不能接受这个，到26日才解决，如果您没有一定的编程基础，爱折腾，坚持不懈的精神，还是不要建自己的 GitHub.io 技术博客吧。

<a name="appendix"/>

## 五、附录

jekyll中文官网：[http://jekyllcn.com/](http://jekyllcn.com/) <br>
Markdown 语法说明：[http://wowubuntu.com/markdown/#autoescape](http://wowubuntu.com/markdown/#autoescape) 

