title: Android之emulator: ERROR: x86 emulation currently requires hardware acceleration
date: 2015-11-19 10:45:38
tags: emulator
category: Android
---
运行AVD，报以下错误：
emulator: ERROR: x86 emulation currently requires hardware acceleration!
Please ensure Intel HAXM is properly installed and usable.
CPU acceleration status: HAX kernel module is not installed!

<!--more-->

解决方案

* BIOS
开机按F2或delete键，进入BIOS，CPU选项，选择Intel Virtualization Technology为enable 
![](http://7q5c2h.com1.z0.glb.clouddn.com/emulatorError1.gif)

* 下载Intel x86 Emulator Accelerator  
 打开 SDK Manager，如果没有Intel x86 Emulator Accelerator ，下载
  ![](http://7q5c2h.com1.z0.glb.clouddn.com/emulatorError3.png)
  
* 安装intelhaxm-android.exe
你的Android sdk安装目录\extras\intel\Hardware_Accelerated_Execution_Manager
 ![](http://7q5c2h.com1.z0.glb.clouddn.com/emulatorError2.png)

* 重启
重启Android Studio，再试下AVD

