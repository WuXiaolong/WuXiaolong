title: Gradle for Android（二）全局设置、自定义BuildConfig、混淆
date: 2016-03-31 14:11:14
tags: Gradle 
category: Gradle 
---
> 系列博客
> [Gradle for Android（一）基本配置、依赖管理](http://wuxiaolong.me/2016/03/30/gradle4android1/)
> [Gradle for Android（三）多渠道打包、配置签名信息](http://wuxiaolong.me/2016/04/01/gradle4android3/)

# 全局设置
如果有很多项目，可以设置全局来统一管理版本号或依赖库，根目录下build.gradle下：
```
ext {
    compileSdkVersion = 23
    buildToolsVersion = "23.0.2"
    minSdkVersion = 14
    targetSdkVersion = 23
}
```
<!--more-->

app/build.gradle
```
android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion

    defaultConfig {
        applicationId "com.wuxiaolong.gradle4android"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
    }
```
可以在根目录下建个config.gradle，然后只需在根目录下build.gradle最顶部加上下面一行代码，然后同步下，意思就是所有的子项目或者所有的modules都可以从这个配置文件里读取内容。
```
apply from: "config.gradle"
```
config.gradle
```
ext {

    android = [
            compileSdkVersion: 23,
            buildToolsVersion: "23.0.2",
            minSdkVersion    : 14,
            targetSdkVersion : 22,

    ]

    dependencies = [
            "appcompat-v7": "com.android.support:appcompat-v7:23.2.1",
            "design"      : "com.android.support:design:23.2.1"

    ]
}

```
app/build.gradle
```
android {
    compileSdkVersion rootProject.ext.android.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion

    defaultConfig {
        applicationId "com.wuxiaolong.gradle4android"
        minSdkVersion rootProject.ext.android.minSdkVersion
        targetSdkVersion rootProject.ext.android.targetSdkVersion
        versionCode 1
        versionName "1.0"
    }
  
...

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile rootProject.ext.dependencies["appcompat-v7"]
    compile rootProject.ext.dependencies["design"]
}
```


# 自定义BuildConfig
实际开发中服务器可能有正式环境和测试环境，gradle可以通过buildConfigField来配置。
```
 defaultConfig {
        buildConfigField 'String','API_SERVER_URL','"http://wuxiaolong.me/"'
    }
```
buildConfigField 一共有3个参数，第一个是数据类型，和Java的类型是对等的；第二个参数是常量名，这里是API_SERVER_URL；第三个参数就是你要配置的值。
![](http://7q5c2h.com1.z0.glb.clouddn.com/gradle4android2.png)
如图路径下就有个常量API_SERVER_URL，如何在代码取得这个常量值：
```
  Log.d("wxl", "API_SERVER_URL=" + BuildConfig.API_SERVER_URL);
```
# 启用proguard混淆
一般release发布版本是需要启用混淆的，这样别人反编译之后就很难分析你的代码，而我们自己开发调试的时候是不需要混淆的，所以debug不启用混淆。对release启用混淆的配置如下：
```
android {

    buildTypes {
        release {
            minifyEnabled true//是否启动混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
   }
}
```
minifyEnabled为true表示启用混淆，proguardFile是混淆使用的配置文件，这里是module根目录下的proguard-rules.pro文件

# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_AndroidProgrammer.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 完整配置
[https://github.com/WuXiaolong/Gradle4Android](https://github.com/WuXiaolong/Gradle4Android)

