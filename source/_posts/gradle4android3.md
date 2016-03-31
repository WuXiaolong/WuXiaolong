title: Gradle for Android（三）多渠道打包、配置签名信息
date: 2016-04-01 14:11:14
tags: Gradle 
category: Gradle 
---
# 系列博客
> [Gradle for Android（一）基本配置、依赖管理](http://wuxiaolong.me/2016/03/30/gradle4android1/)
> [Gradle for Android（二）全局设置、自定义BuildConfig、混淆](http://wuxiaolong.me/2016/03/31/gradle4android2/)

# 多渠道打包
国内有太多Android App市场，每次发版几十个渠道包。还好Android Gradle给我们提供了productFlavors，我们可以对生成的APK包进行定制。
```
    productFlavors {//多渠道打包
        xiaomi {
            applicationId 'com.wuxiaolong.gradle4android1'
        }

        googlepaly {
            applicationId 'com.wuxiaolong.gradle4android2'
        }
    }
```
<!--more-->

## 定制生成的apk文件名
```
   applicationVariants.all { variant ->
                if (variant.buildType.name.equals('release')) {
                    variant.outputs.each { output ->
                        def outputFile = output.outputFile
                        if (outputFile != null && outputFile.name.endsWith('.apk')) {
                            def fileName = "gradle4android_v${defaultConfig.versionName}_${releaseTime()}_${variant.flavorName}.apk"
                            output.outputFile = new File(outputFile.parent, fileName)
                        }
                    }
                }
            }
```
输出apk名字：gradle4android_v1.0_2016-03-23_xiaomi.apk

## 占位符
多渠道打包，还会遇到一个问题，比如友盟统计的渠道号，Gradle处理办法：manifestPlaceholders，它允许我们动态替换我们在AndroidManifest文件里定义的占位符。
AndroidManifest.xml：
```
 <meta-data
            android:name="UMENG_CHANNEL"
            android:value="${UMENG_CHANNEL_VALUE}" />
```
如下，${UMENG_CHANNEL_VALUE}占位符会被dev替换。
```
 defaultConfig {
        manifestPlaceholders = [UMENG_CHANNEL_VALUE: 'dev']
    }
```
如果渠道太多，不用这样一个个去写，可以循环：
```
 productFlavors.all { flavor ->
                manifestPlaceholders.put("UMENG_CHANNEL_VALUE",name)
            }
```
## 渠道打包完整代码：
```
android {
//省略部分代码
     buildTypes {
        release {
            minifyEnabled false//是否启动混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            applicationVariants.all { variant ->
                if (variant.buildType.name.equals('release')) {
                    variant.outputs.each { output ->
                        def outputFile = output.outputFile
                        if (outputFile != null && outputFile.name.endsWith('.apk')) {
                            def fileName = "gradleTest_v${defaultConfig.versionName}_${releaseTime()}_${variant.flavorName}.apk"
                            output.outputFile = new File(outputFile.parent, fileName)
                        }
                    }
                }
            }
            //针对很多渠道
            //productFlavors.all { flavor ->
            //   manifestPlaceholders.put("UMENG_CHANNEL_VALUE",name)
            // }
        }
    }
    productFlavors {//多渠道打包，命令行打包：gradlew assembleRelease
        xiaomi {
            applicationId 'com.wuxiaolong.gradle4android1'
            manifestPlaceholders.put("UMENG_CHANNEL_VALUE", 'xiaomi')
        }
        googlepaly {
            applicationId 'com.wuxiaolong.gradle4android2'
            manifestPlaceholders.put("UMENG_CHANNEL_VALUE", 'googlepaly')
        }
    }
 //省略部分代码

def releaseTime() {
    return new Date().format("yyyy-MM-dd", TimeZone.getTimeZone("UTC"))
}
```

# 配置签名信息
## Android Studio设置默认的签名文件
新浪微博SSO登录，微信分享这些都需要签名打包，才能看到效果，设置默认签名文件为自己的签名jks，这样就不需要打包了直接运行起来就是正式的签名。
在android.signingConfigs{}下定义一个或者多个签名信息，然后在buildTypes{}配置使用即可。
在app目录下添加你的.jks，然后app的build.gradle文件中的增加以下内容：
第一种：
```
android {  
    signingConfigs {  
        debug {  
            storeFile file("WuXiaolong.jks")
            storePassword 'android'
            keyAlias 'android'
            keyPassword 'android'
        }          
    }  	
}
```
buildTypes没有配置，也是直接取得debug，是不是不配置默认取得是debug呢？
第二种：
```
android {  
    signingConfigs {  
        release {  
            storeFile file("WuXiaolong.jks")
            storePassword 'android'
            keyAlias 'android'
            keyPassword 'android'
        }          
    }  
	buildTypes {
        debug {
            signingConfig signingConfigs.release
        }        
    }
}
```
# 签名打包
## 通过Android Studio签名
这里不细说了。
## 通过命令行签名
如上那样配置签名信息
```
android {  
    signingConfigs {  
        release {  
            storeFile file("WuXiaolong.jks")
            storePassword 'android'
            keyAlias 'android'
            keyPassword 'android'
        }          
    }  
	buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.release
        }      
    }
}
```
先"build-clean Project"，然后Terminal输入命名行：
```
gradlew assembleRelease
```
打印信息如下：
```
E:\AndroidStudioProjects\Gradle4Android>gradlew assembleRelease
:app:preBuild UP-TO-DATE                                                             
:app:preReleaseBuild UP-TO-DATE     
:app:checkReleaseManifest                  
//省略部分               
:app:packageRelease                 
:app:zipalignRelease                 
:app:assembleRelease                 
               
BUILD SUCCESSFUL
```
OK，打包成功的apk路径如：E:\AndroidStudioProjects\Gradle4Android\app\build\outputs\apk\app-release.apk
![](http://7q5c2h.com1.z0.glb.clouddn.com/gradle4android3.png)

# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_AndroidProgrammer.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 完整配置
[https://github.com/WuXiaolong/Gradle4Android](https://github.com/WuXiaolong/Gradle4Android)

# 附录
[Gradle for Android 第一篇( 从 Gradle 和 AS 开始 )](https://segmentfault.com/a/1190000004229002)
[使用Gradle管理你的Android Studio工程](http://www.flysnow.org/2015/03/30/manage-your-android-project-with-gradle.html)

