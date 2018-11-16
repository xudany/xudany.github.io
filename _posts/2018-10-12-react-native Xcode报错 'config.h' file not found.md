---
title: react-native Xcode报错'config.h' file not found
date: 2018-10-12
categories:
- react-native 
tags:
- react-native
- Xcode
- issue
---



问题：Xcode报错'config.h' file not found......

原因：config.h的文件没有找到，可能是项目引用有问题

解决方法：

在项目中执行以下命令，这个命令是check .h头文件的引用情况，然后建立关联关系

```
cd node_modules/react-native/third-party/glog-0.3.4
../../scripts/ios-configure-glog.sh12
```

然后点击Xcode菜单栏Product -->Clean Build Folder ，再重新编译，问题解决~