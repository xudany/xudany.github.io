---
title: fail to push some refs to git 
date: 2017-10-23 17:29:08
categories:
- git
tags:
- git 
- issue
---

# 关于fail to push some refs to git解决方法

## 执行
> git add .
> git commit -m ""
> git push

出现了fail to push some refs to git的问题

解决方法：上网搜索，出现各种解决方法，逐个尝试

最终解决方法：

使用强制提交覆盖

>git push -f

出现问题原因：本地之前有部分代码进行了提交

