title: AndroidStudio常见错误
date: 2015-07-21 09:26:11
tags: Android Studio
category: Android Studio
---
> Android Studio导入项目，可能会有各种错误，这里记录我开发遇到的错误及解决方案。供参考。

# org.gradle.api.internal.project.ProjectInternal.getPluginManager
```java
repositories {
    jcenter()
}

dependencies {
    classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'

    // this was apply plugin android-maven
    apply plugin: 'com.github.dcendents.android-maven'
}
```
<!--more-->
## 报错：
Error:(2, 1) A problem occurred evaluating project ':library'.
org.gradle.api.internal.project.ProjectInternal.getPluginManager()Lorg/gradle/api/internal/plugins/PluginManagerInternal;
## 解决方案：
工程文件目录gradle\wrapper里面找到gradle-wrapper.properties修改里面的值：
将distributionUrl=https\://services.gradle.org/distributions/gradle-2.2.1-all.zip改为distributionUrl=https\://services.gradle.org/distributions/gradle-2.8-all.zip

# Error:Execution failed for task ':app:transformClassesWithDexForDebug'.
```xml
com.android.build.api.transform.TransformException: com.android.ide.common.process.ProcessException: org.gradle.process.internal.ExecException: Process 'command 'D:\Program Files\Java\jdk1.8.0_45\bin\java.exe'' finished with non-zero exit value 2
```

## 解决方案：
```xml
android {
   ...
   defaultConfig {
      ...
      multiDexEnabled true
   }
}
```

报错的意思，jar包重复了，我依赖了项目，我的app也有这个jar。我增加了这行，但是还是报错，于是我删除我的app的jar引用，还是报错了，折腾了一会，把我的app的jar删掉了，终于解决。