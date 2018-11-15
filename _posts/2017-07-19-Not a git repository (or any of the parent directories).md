---
title: Not a git repository (or any of the parent directories)
date: 2017-07-19
categories:
- git 
tags:
- git
- issue
---



第一次用git，遇到这个错误提示:

```
fatal: Not a git repository (or any of the parent directories): .git

This tells you that the directory you're in is not a git repository. Before you can add remote servers, commit things and so you must have a git repository created.

In a git repository there's a directory (which can be hidden) named .git. It contains metadata on the repository and all the data regarding the checked files.

To create it type git init in the directory you wish to have a git repository. Then you could add remote servers and perform operations on it.
```



提示说没有找到一个.git这样的目录，解决办法如下：

```
git init
```

