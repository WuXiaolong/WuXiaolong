title: 手把手教你建github技术博客by hexo
date: 2015-07-31 14:22:35
tags: [github,hexo]
category: hexo
---
# 适合人群

* 喜欢写Blog的人
* 有一定的编程基础
* 爱折腾的人
* 熟练使用版本控制Git
* 了解使用Github
* 熟悉基本的MarkDown语法

<!-- more -->

# 环境准备

## 安装Git
下载 [msysgit](http://msysgit.github.io/) 并执行即可完成安装。

## 安装Node.js
在 Windows 环境下安装 [Node.js](http://nodejs.org/) 非常简单，仅须下载安装文件并执行即可完成安装。

## 安装hexo
利用 npm 命令即可安装。（在任意位置点击鼠标右键，选择Git bash）
```js
npm install -g hexo
```
`问题`
* npm ERR! registry error parsing json 错误

可能需要设置npm代理,执行命令
```js 
npm config set registry http://registry.cnpmjs.org
```

* hexo:command not found
删除刚刚安装的npm目录，重新执行命令npm install -g hexo安装hexo，


## 创建hexo文件夹
安装完成后，在你喜爱的文件夹下（如H:\hexo），执行以下指令(在H:\hexo内点击鼠标右键，选择Git bash)，Hexo 即会自动在目标文件夹建立网站所需要的所有文件。
```js
hexo init
```

## 安装依赖包
```js
npm install
```

## 本地查看

现在我们已经搭建起本地的hexo博客了，执行以下命令(在H:\hexo)，然后到浏览器输入localhost:4000看看。
```js
hexo generate
hexo server
```
好了，至此，本地博客已经搭建起来了，只是本地哦，别人看不到的。下面，我们要部署到Github。

`问题`
* 执行hexo server提示找不到该指令 
解决办法： 
在Hexo 3.0 后server被单独出来了，需要安装server，安装的命令如下：
```js
npm install hexo -server --save 
```
安装此server后再试，问题解决

# github 创建博客

* ## 注册账号
地址：[https://github.com/](https://github.com/)  
输入账号、邮箱、密码,然后点击注册按钮.
![1](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-1.png)

## 创建页面仓库
这个仓库的名字需要和你的账号对应，格式: yourname.github.io
输入基本信息，然后点击创建仓库.
![2](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-2.png)
![3](http://7q5c2h.com1.z0.glb.clouddn.com/2014-10-26-build-blog-3.png)
`注意`
命名规则：你的github账号.github.io，我这里被坑了，之前是jekell写的，现在换成hexo，所以我是另建创库了。

## 生成SSH密钥
ssh-keygen -t rsa -C "你的邮箱地址"，按3个回车，密码为空。

在C:\Users\Administrator\.ssh下，得到两个文件id_rsa和id_rsa.pub。

## 在GitHub上添加SSH密钥
打开id_rsa.pub，复制全文。[https://github.com/settings/ssh](https://github.com/settings/ssh) ，Add SSH key，粘贴进去。

# hexo使用

## 目录结构

.
├── .deploy       #需要部署的文件
├── node_modules  #Hexo插件
├── public        #生成的静态网页文件
├── scaffolds     #模板
├── source        #博客正文和其他源文件，404、favicon、CNAME 都应该放在这里
|   ├── _drafts   #草稿
|   └── _posts    #文章
├── themes        #主题
├── _config.yml   #全局配置文件
└── package.json

## 全局配置 _config.yml

```js
# Hexo Configuration
# Docs: http://hexo.io/docs/configuration.html
# Source: https://github.com/hexojs/hexo/
# Site #站点信息
title:  #标题
subtitle:  #副标题
description:  #站点描述，给搜索引擎看的
author:  #作者
email:  #电子邮箱
language: zh-CN #语言
# URL #链接格式
url:  #网址
root: / #根目录
permalink: :year/:month/:day/:title/ #文章的链接格式
tag_dir: tags #标签目录
archive_dir: archives #存档目录
category_dir: categories #分类目录
code_dir: downloads/code
permalink_defaults:
# Directory #目录
source_dir: source #源文件目录
public_dir: public #生成的网页文件目录
# Writing #写作
new_post_name: :title.md #新文章标题
default_layout: post #默认的模板，包括 post、page、photo、draft（文章、页面、照片、草稿）
titlecase: false #标题转换成大写
external_link: true #在新选项卡中打开连接
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
highlight: #语法高亮
  enable: true #是否启用
  line_number: true #显示行号
  tab_replace:
# Category & Tag #分类和标签
default_category: uncategorized #默认分类
category_map:
tag_map:
# Archives
2: 开启分页
1: 禁用分页
0: 全部禁用
archive: 2
category: 2
tag: 2
# Server #本地服务器
port: 4000 #端口号
server_ip: localhost #IP 地址
logger: false
logger_format: dev
# Date / Time format #日期时间格式
date_format: YYYY-MM-DD #参考http://momentjs.com/docs/#/displaying/format/
time_format: H:mm:ss
# Pagination #分页
per_page: 10 #每页文章数，设置成 0 禁用分页
pagination_dir: page
# Disqus #Disqus评论，替换为多说
disqus_shortname:
# Extensions #拓展插件
theme: landscape-plus #主题
exclude_generator:
plugins: #插件，例如生成 RSS 和站点地图的
- hexo-generator-feed
- hexo-generator-sitemap
# Deployment #部署，将 lmintlcx 改成用户名
deploy:
  type: git
  repo: 刚刚github创库地址.git
  branch: master
```
`注意`
* 配置文件的冒号“:”后面有一个空格
* repo: 刚刚github创库地址.git

## hexo命令行使用

常用命令：

```js
hexo help #查看帮助
hexo init #初始化一个目录
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成网页，可以在 public 目录查看整个网站的文件
hexo server #本地预览，'Ctrl+C'关闭
hexo deploy #部署.deploy目录
hexo clean #清除缓存，**强烈建议每次执行命令前先清理缓存，每次部署前先删除 .deploy 文件夹**
```

简写：

```js
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

## 编辑文章

新建文章

```js
hexo new "标题"
```
在 _posts 目录下会生成文件标题.md
```js
title: Hello World
date: 2015-07-30 07:56:29 #发表日期，一般不改动
categories: hexo #文章文类
tags: [hexo,github] #文章标签，多于一项时用这种格式
---
正文，使用Markdown语法书写
```
编辑完后保存，hexo server 预览

# hexo部署

执行下列指令即可完成部署。
```js
hexo generate
hexo deploy
```
hexo deploy问题：Deployer not found: git
执行
```java
npm install hexo-deployer-git --save
```
重新deploy即可

以下提示说明部署成功
```js
[info] Deploy done: git
```
点击 Github 上项目的 Settings，GitHub Pages，提示Your site is published at http://wuxiaolong.me (这是我买的域名)

# 图床
1.墙裂推荐七牛云储存,[注册地址](https://portal.qiniu.com/signup?code=3lk4saqepkdhu)。

2.七牛云储存提供10G的免费空间,以及每月10G的流量.存放个人博客图片最好不过了

3.七牛云储存还有各种图形处理功能、缩略图、视频存放速度也给力（非打广告）。

具体使用见[使用七牛作为github博客的图床](http://wuxiaolong.github.io/blog/2014/10/30/qiniu-photo-bed.html)

# 域名
## 将独立域名与GitHub Pages的空间绑定
方法一：在站点source目录下面，新建一个名为CNAME的文本文件，里面写入你要绑定的域名，比如wuxiaolong.me
方法二：在Repository的根目录下面，新建一个名为CNAME的文本文件，里面写入你要绑定的域名，比如wuxiaolong.me

## DNS设置
用[DNSpod](https://www.dnspod.cn/)，快，免费，稳定。
注册DNSpod，添加域名，如下图设置。
![](http://7q5c2h.com1.z0.glb.clouddn.com/build-blog-by-hexo-1.png)
其中A的两条记录指向的ip地址是github Pages的提供的ip
如何知道你的github上项目的IP，如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/build-blog-by-hexo-2.png)

## 去Godaddy修改DNS地址
更改godaddy的Nameservers为DNSpod的NameServers。
![](http://7q5c2h.com1.z0.glb.clouddn.com/build-blog-by-hexo-5.png)

# 插件
> 2016.08.20更新

## 安装插件
安装插件：npm install 插件名 –save
卸载插件：npm uninstall 插件名
更新插件和博客框架：npm update

执行以下命令安装 RSS 插件
```
npm install hexo-generator-feed --save
```
生成站点地图
```
npm install hexo-generator-sitemap --save
```
生成百度站点地图
```
npm install hexo-generator-baidu-sitemap --save
```
SEO优化
```
npm install hexo-generator-seo-friendly-sitemap --save
```
HTML, CSS, JS 和 imagages压缩
```
npm install hexo-all-minifier --save
```
## 插件开启配置
根目录下的 _config.yml，添加以下代码:
```
# RSS
feed:
type: atom
path: atom.xml
limit: 20

# sitemap
# 提交给谷歌搜素引擎,SEO优化开启配置是一样的
sitemap:
    path: sitemap.xml
# 提交百度搜索引擎   
baidusitemap:
    path: baidusitemap.xml 

# HTML压缩
html_minifier:
  enable: true
  exclude:     
# css压缩
css_minifier:
  enable: true
  exclude: 
    - '*.min.css'
# js压缩   
js_minifier:
  enable: true
  mangle: true
  output:
  compress:
  exclude: 
    - '*.min.js'
# image压缩    
image_minifier:
  enable: true
  interlaced: false
  multipass: false
  optimizationLevel: 2
  pngquant: false
  progressive: false   
```
node_modules目录可以看安装了哪些插件。
[Hexo Seo优化让你的博客在google搜索排名第一](http://www.jianshu.com/p/86557c34b671)
[搭建 Hexo 博客--增强篇](http://www.jianshu.com/p/2640561e96f8)



# 总结
之前用的jekell写的，[手把手教你建github技术博客by jekyll](http://wuxiaolong.github.io/2014/10/26/build-blog/)，也是折腾了几天才做成自己满意的，昨天决定换成hexo，也是花了一天半时间，为了追求更好，必须折腾！


# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 参考文献
* [hexo系列教程：（一）hexo介绍](http://zipperary.com/2013/05/28/hexo-guide-1/)
* [使用Hexo搭建博客](http://blog.lmintlcx.com/post/blog-with-hexo.html)
* [如何搭建一个独立博客——简明Github Pages与Hexo教程](http://blog.csdn.net/poem_of_sunshine/article/details/29369785)
* [windows下搭建hexo博客并将其部署到GitCafe终极教程](http://www.jianshu.com/p/e858a90d0a17)
* [GoDaddy优惠码](http://www.dute.me/)
* [Godaddy购买域名及配置](http://blog.csdn.net/poem_of_sunshine/article/details/29369785)
* [GitHub配置域名（Godaddy）](http://blog.csdn.net/yuguiyang1990/article/details/39523005)
* [hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)
* [hexo官网](https://hexo.io/zh-cn/)



