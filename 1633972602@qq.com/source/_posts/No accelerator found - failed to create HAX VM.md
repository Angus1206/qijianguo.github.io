---
title: No accelerator found - failed to create HAX VM
categories: Java
tags: java
---

```

13:56	Executing tasks: [:app:assembleDebug]

13:56	Emulator: Incompatible HAX module version 3,requires minimum version 4

13:56	Emulator: No accelerator found.

13:56	Emulator: D:\develop\android\android-sdk-windows\emulator\qemu\windows-x86_64\qemu-system-i386.exe: failed to initialize HAX: Invalid argument

13:56	Emulator: Process finished with exit code 0
```
完美解决：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190108140759101.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)


参考：https://stackoverflow.com/questions/39087532/no-accelerator-found-failed-to-create-hax-vm
