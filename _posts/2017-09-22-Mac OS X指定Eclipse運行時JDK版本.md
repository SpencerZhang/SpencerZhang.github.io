---
layout:     post
title:      Mac OS X指定Eclipse運行時JDK版本
subtitle:   指定Eclipse運行時JDK版本
date:       2017-09-22
author:     Spencer
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - Eclipse
    - Mac
    - JDK
---

# Mac OS X指定Eclipse運行時JDK版本

## 執行命令/usr/libexec/java_home -V 查看當前系統已安裝JDK列表
```
❯ /usr/libexec/java_home -V
Matching Java Virtual Machines (3):
    9, x86_64:	"Java SE 9"	/Library/Java/JavaVirtualMachines/jdk-9.jdk/Contents/Home
    1.8.0_74, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_74.jdk/Contents/Home
    1.7.0_51, x86_64:	"Java SE 7"	/Library/Java/JavaVirtualMachines/jdk1.7.0_51.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk-9.jdk/Contents/Home
```

## /usr/libexec/java_home文件信息

```
❯ ls -ltr /usr/libexec/java_home
lrwxr-xr-x  1 root  wheel  79 Sep 21  2016 /usr/libexec/java_home -> /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java_home
```

## 修改eclipse.ini,目錄/Applications/Eclipse.app/Contents/Eclipse/
在-vmargs參數之前添加
```
-vm
/Library/Java/JavaVirtualMachines/jdk1.8.0_74.jdk/Contents/Home/bin/java
-vmargs
```