title: Android֮emulator: ERROR: x86 emulation currently requires hardware acceleration
date: 2015-11-19 10:45:38
tags: emulator
category: Android
---
����AVD�������´���
emulator: ERROR: x86 emulation currently requires hardware acceleration!
Please ensure Intel HAXM is properly installed and usable.
CPU acceleration status: HAX kernel module is not installed!

<!--more-->

�������

* BIOS
������F2��delete��������BIOS��CPUѡ�ѡ��Intel Virtualization TechnologyΪenable 
![](http://7q5c2h.com1.z0.glb.clouddn.com/emulatorError1.gif)

* ����Intel x86 Emulator Accelerator  
 �� SDK Manager�����û��Intel x86 Emulator Accelerator ������
  ![](http://7q5c2h.com1.z0.glb.clouddn.com/emulatorError3.png)
  
* ��װintelhaxm-android.exe
���Android sdk��װĿ¼\extras\intel\Hardware_Accelerated_Execution_Manager
 ![](http://7q5c2h.com1.z0.glb.clouddn.com/emulatorError2.png)

* ����
����Android Studio��������AVD

