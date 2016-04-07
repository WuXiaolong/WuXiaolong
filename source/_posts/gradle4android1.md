title: Gradle for Android（一）基本配置、依赖管理
date: 2016-03-30 14:11:14
tags: Gradle 
category: Gradle 
---
> Gradle是一种基于Groovy的动态DSL，而Groovy语言是一种基于jvm的动态语言。这里只分享实际开发中会用到的场景，您不需要去学习Groovy语言，知道Java的您是很容易阅读Groovy语言的。
> 系列博客涉及的知识点有：Gradle基本配置、依赖管理、全局设置、自定义BuildConfig、混淆、多渠道打包、配置签名信息、单元测试，是不是迫不及待了啊，快来学习学习。

# 基本配置
新建项目，目录结构如下：
![](http://7q5c2h.com1.z0.glb.clouddn.com/gradle4android1.png)
<!--more-->
## app/build.gradle
初始化的Gradle配置：
```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"

    defaultConfig {
        applicationId "com.wuxiaolong.gradle4android"
        minSdkVersion 15
        targetSdkVersion 23
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
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.2.1'
    compile 'com.android.support:design:23.2.1'
}
```

```
apply plugin: ‘com.android.application’，表示该module是一个app module，应用了com.android.application插件，如果是一个android library，那么这里写apply plugin: ‘com.android.library’

compileSdkVersion：基于哪个SDK编译，这里是API LEVEL

buildToolsVersion：基于哪个构建工具版本进行构建的。

defaultConfig：默认配置，如果没有其他的配置覆盖，就会使用这里的。
applicationId：配置包名的
versionCode：版本号
versionName：版本名称

buildTypes是构建类型，常用的有release和debug两种，可以在这里面启用混淆，启用zipAlign以及配置签名信息等。

dependencies：不属于Android专有的配置了，它定义了该module需要依赖的jar，aar，jcenter库信息。
```
## gradle-wrapper.properties
声明了gradle的目录与下载路径以及当前项目使用的gradle版本，这些默认的路径我们一般不会更改的
```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-2.8-all.zip

```

## 根目录的build.gradle
定义在这个工程下的所有模块的公共属性
```
buildscript {
    repositories {
        jcenter()//使用jcenter库
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.5.0'// 依赖android提供的1.5.0的gradle build

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
//为所有的工程的repositories配置为jcenters
allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

```

## setting.gradle
包含哪些模块，比如有app和library：
```
include ':app',':library'
```

# 依赖管理
## 本地依赖
### jar
默认情况下，新建的Android项目会有一个lib文件夹
```
dependencies {
       compile fileTree(dir: 'libs', include: ['*.jar'])//即添加所有在libs文件夹中的jar
       //compile files('libs/WuXiaolong.jar')//不需要这样一个个去写了
}
```
### so包
用c或者c++写的library会被叫做so包，Android插件默认情况下支持native包，你需要把.so文件放在对应的文件夹中
```
app
   ├── AndroidManifest.xml
   └── jniLibs
       ├── armeabi
       │   └── WuXiaolong.so
       ├── armeabi-v7a
       │   └── WuXiaolong.so
       ├── mips
       │   └── WuXiaolong.so
       └── x86
           └── WuXiaolong.so
```
### aar文件
library库输出文件是.aar文件，在library工程build/output/aar/下

直接依赖library
```
dependencies {
       compile project(':library名字')
       compile project(':libraries:library名字')//多个library，libraries是文件夹名字
  }
```

依赖.aar文件
创建一个aars文件夹，然后把.aar文件拷贝到该文件夹里面，然后添加该文件夹作为依赖库：
```
repositories {
    flatDir {
        dirs 'aars' 
    }
}
```
或者：
```
 dependencies {
       compile(name:'libraryname', ext:'aar')
}
```
## 远程仓库
```
dependencies {
		compile 'com.wuxiaolong.pullloadmorerecyclerview:library:1.0.4'
}
```
# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_AndroidProgrammer.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 完整配置
[https://github.com/WuXiaolong/Gradle4Android](https://github.com/WuXiaolong/Gradle4Android)

# 系列博客
[Gradle for Android（二）全局设置、自定义BuildConfig、混淆](http://wuxiaolong.me/2016/03/31/gradle4android2/)
[Gradle for Android（三）多渠道打包、配置签名信息](http://wuxiaolong.me/2016/04/01/gradle4android3/)



