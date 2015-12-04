title: Android之emulator ERROR x86 emulation currently requires hardware acceleration
date: 2015-12-03 10:45:38
tags: emulator
category: Android
---
一直在用Genymotion模拟器，话说Android studio将出2.0，速度提速50倍，这是要断Genymotion的路，于是我在家里电脑运行AVD，却报以下错误：
emulator: ERROR: x86 emulation currently requires hardware acceleration!
Please ensure Intel HAXM is properly installed and usable.
CPU acceleration status: HAX kernel module is not installed!

<!--more-->

## 解决方案
折腾一番，解决方案如下：

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

## 附录
1. [Emulator: ERROR: x86 emulation currently requires hardware acceleration](http://stackoverflow.com/questions/29136173/emulator-error-x86-emulation-currently-requires-hardware-acceleration)
2. [Error in launching AVD](http://stackoverflow.com/questions/26355645/error-in-launching-avd)

