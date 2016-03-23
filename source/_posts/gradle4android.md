title: Gradle for Android
date: 2016-03-31 14:11:14
tags: Gradle 
category: Android
---
---
作者：吴小龙同學
转载请注明出处：
---
# Android Gradle基本配置
新建项目，已经给你一个初始化的Gradle配置：
```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"

    defaultConfig {
        applicationId "com.wuxiaolong.gradletest"
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
apply plugin: ‘com.android.application’，表示该module是一个app module，应用了com.android.application插件，如果是一个android library，那么这里写apply plugin: ‘com.android.library’

compileSdkVersion：基于哪个SDK编译，这里是API LEVEL

buildToolsVersion：基于哪个构建工具版本进行构建的。

defaultConfig：默认配置，如果没有其他的配置覆盖，就会使用这里的。
applicationId：配置包名的
versionCode：版本号
versionName：版本名称

buildTypes是构建类型，常用的有release和debug两种，可以在这里面启用混淆，启用zipAlign以及配置签名信息等。

dependencies：不属于Android专有的配置了，它定义了该module需要依赖的jar，aar，jcenter库信息。

再来看个较完整的配置：
```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"

    defaultConfig {
        //applicationId "com.wuxiaolong.gradletest"
        minSdkVersion 15
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false//是否启动混淆
            zipAlignEnabled true//是否启动zipAlign       
            shrinkResources true // 是否移除无用的resource文件
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    productFlavors {//多渠道打包
        xiaomi {
            applicationId 'com.wuxiaolong.gradletest1'
        }

        googlepaly {
            applicationId 'com.wuxiaolong.gradletest2'
        }
    }
    signingConfigs {//签名的配置
        debug {
            storeFile file("你的.jks")
            storePassword 'android'
            keyAlias 'android'
            keyPassword 'android'
        }
    }
    lintOptions {//设置编译的lint开关，程序在buid的时候，会执行lint检查，有任何的错误或者警告提示，都会终止构建
        abortOnError false
    }
    compileOptions {//可以进行 Java 的版本配置，以便使用对应版本的一些新特性,让IDE使用JAVA8语法解析
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.2.1'
    compile 'com.android.support:design:23.2.1'
}

```

# 依赖管理
## 远程仓库
## 本地依赖

# 全局设置
如果有很多项目，可以设置全局来统一管理版本号，根目录下build.gradle下：
```
ext {
    compileSdkVersion = 23
    buildToolsVersion = "23.0.2"
    minSdkVersion = 14
    targetSdkVersion = 23
}
```
app/build.gradle
```
android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion

    defaultConfig {
        applicationId "com.wuxiaolong.gradletest"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
    }
```
可以在根目录下建个config.gradle，然后只需在根目录下build.gradle最顶部加上下面一行代码，意思就是所有的子项目或者所有的modules都可以从这个配置文件里读取内容。
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
        applicationId "com.wuxiaolong.gradletest"
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

# 多渠道打包
国内有太多Android App市场，每次发版几十个渠道包。还好Android Gradle给我们提供了productFlavors，我们可以对生成的APK包进行定制。
```
    productFlavors {//多渠道打包
        xiaomi {
            applicationId 'com.wuxiaolong.gradletest1'
        }

        googlepaly {
            applicationId 'com.wuxiaolong.gradletest2'
        }
    }
```

## 定制生成的apk文件名
```
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
```
输出apk名字：gradleTest_v1.0_2016-03-23_xiaomi.apk

## 占位符
多渠道打包，还会一个问题，比如友盟统计，Gradle处理办法，那就是manifestPlaceholders，它允许我们动态替换我们在AndroidManifest文件里定义的占位符。AndroidManifest.xml：
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
    productFlavors {//多渠道打包
        xiaomi {
            applicationId 'com.wuxiaolong.gradletest1'
            manifestPlaceholders.put("UMENG_CHANNEL_VALUE", 'xiaomi')
        }
        googlepaly {
            applicationId 'com.wuxiaolong.gradletest2'
            manifestPlaceholders.put("UMENG_CHANNEL_VALUE", 'googlepaly')
        }
    }
 //省略部分代码

def releaseTime() {
    return new Date().format("yyyy-MM-dd", TimeZone.getTimeZone("UTC"))
}
```


