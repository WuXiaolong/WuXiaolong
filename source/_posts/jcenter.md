title: Android library分享到jcenter
date: 2015-11-12 15:30:50
tags: jcenter
category: Android
---
```java
dependencies {
  compile 'com.wuxiaolong.pullloadmorerecyclerview:library:1.0.1'
}
```
build.gradle文件中只写上如上一行，就能在Android Studio中引入你的library到项目中，这样岂不是酷比了。之前github上分享一个简单的库，这两天花了点时间研究了下，如何也能这样一行就能依赖我的那个库。
<!--more-->
## 申请bintray账号
jcenter是一个由 [bintray.com](https://bintray.com/)维护的Maven仓库 。
在[bintray.com](https://bintray.com/)上注册一个账号。然后点击maven。点击Add New Package，为我们的library创建一个新的package。命名所有字母应该为小写，单词之间用－分割。

## 配置
library项目最好分成两个module，一个Application Module，一个Library Module。Application Module用于展示库的用法，Library Module是library的源代码。

### 配置项目
修改项目的build.gradle文件中的依赖部分
```java
dependencies {
        classpath 'com.android.tools.build:gradle:1.3.0'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.2'
        classpath 'com.github.dcendents:android-maven-plugin:1.2'
    }
```

打开项目的local.properties，加入以下两句:
```java
bintray.user=your_user_name
bintray.apikey=your_apikey
```
bintray username 放在第一行， API Key放在第二行， API Key为你bintray的账户的apikey，可以点击进入你的账户信息里再点击Edit即有查看API Key的选项，把他复制下来。
这些信息时比较敏感的，不应该到处分享。幸而local.properties文件就已经被添加到.gitignore了。因此这些敏感数据不会被误传到git服务器。

## 配置library
很多，具体配置可参考我的开源库[PullLoadMoreRecyclerView](https://github.com/WuXiaolong/PullLoadMoreRecyclerView/blob/master/library/build.gradle)。
```java
apply plugin: 'com.android.library'
apply plugin: 'com.github.dcendents.android-maven'
apply plugin: 'com.jfrog.bintray'

// 这个version是区分library版本的，因此当我们需要更新library时记得修改这个version, 这个version影响后面的引用
version = "1.0.1"

android {
    compileSdkVersion 22
    buildToolsVersion "22.0.1"
    resourcePrefix "PullLoadMoreRecyclerView"	//这个随便填
    defaultConfig {
        minSdkVersion 14
        targetSdkVersion 22
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    compile 'com.android.support:appcompat-v7:22.2.0'
    compile 'com.android.support:recyclerview-v7:22.2.0'

}

def siteUrl = 'https://github.com/WuXiaolong/PullLoadMoreRecyclerView'      // 项目的主页
def gitUrl = 'https://github.com/WuXiaolong/PullLoadMoreRecyclerView.git'   // Git仓库的url
group = "com.wuxiaolong.pullloadmorerecyclerview"                                        // Maven Group ID for the artifact，一般填你唯一的包名
install {
    repositories.mavenInstaller {
        // This generates POM.xml with proper parameters
        pom {
            project {
                packaging 'aar'
                // Add your description here
                name 'RecyclerView实现下拉刷新和上拉加载更多以及瀑布流效果' 	//项目描述
                url siteUrl
                // Set your license
                licenses {
                    license {
                        name 'The Apache Software License, Version 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
                developers {
                    developer {
                        id 'WuXiaolong'		//填写的一些基本信息
                        name 'WuXiaolong'
                        email '1413129987@qq.com'
                    }
                }
                scm {
                    connection gitUrl
                    developerConnection gitUrl
                    url siteUrl
                }
            }
        }
    }
}
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}
task javadoc(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}
artifacts {
    archives javadocJar
    archives sourcesJar
}
Properties properties = new Properties()
properties.load(project.rootProject.file('local.properties').newDataInputStream())
bintray {
    user = properties.getProperty("bintray.user")
    key = properties.getProperty("bintray.apikey")
    configurations = ['archives']
    pkg {
        repo = "maven"
        name = "pullloadmorerecyclerview"	//发布到JCenter上的项目名字
        websiteUrl = siteUrl
        vcsUrl = gitUrl
        licenses = ["Apache-2.0"]
        publish = true
    }
}



```
## 上传library到bintray空间
请到Android Studio的终端（Terminal）选项卡。
检查代码的正确性，输入下面的命令：

```js
gradlew install
```
如果没有什么问题，会显示：
```js
BUILD SUCCESSFUL
```
上传编译的文件到bintray，使用如下的命令：
```js
gradlew bintrayUpload
```

如果显示如下你就大喊一声eureka吧！
```js
BUILD SUCCESSFUL
```
![](http://7q5c2h.com1.z0.glb.clouddn.com/includeMyPackage.png)
上传完成即可在Bintray网站上找到你的Repo，我们需要完成最后一步工作，申请你的Repo添加到JCenter。可以进入[这个页面](https://bintray.com/bintray/jcenter)，点击Include My Package，输入你的项目名字，点击匹配到的项目，直接send即可，然后就等管理员批准了。
![](http://7q5c2h.com1.z0.glb.clouddn.com/jcenterApproved.png)
如上，网站上会给你一条通过信息，然后就OK了，大功告成。

```java
compile 'com.wuxiaolong.pullloadmorerecyclerview:library:1.0.1'
```
一般来说，我们需要知道library的字符串形式，包含3部分
GROUP_ID:ARTIFACT_ID:VERSION
上面的例子中，GROUP_ID是com.wuxiaolong.pullloadmorerecyclerview ，即配置library中group的值；ARTIFACT_ID是library，配置library中没有写到，是library项目的名字；VERSION是1.0.1。
查看bintray上库：
http://jcenter.bintray.com/com/wuxiaolong/pullloadmorerecyclerview/library/1.0.1/

## 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>

## 附录
以下博客有的地方行不通或不够详细，酌情参考。
[如何使用Android Studio把自己的Android library分享到jCenter和Maven Central](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0623/3097.html)
[将Library上传到Jcenter](http://www.jianshu.com/p/0ba8960f80a9)
