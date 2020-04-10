---
title: react + ts + next.js + koa
date: 2020-04-08
categories:
- react
tags:
- react
- ts
- next.js
- koa
---



该来的总会来，后端渲染学习走起。啦啦啦(～￣▽￣)～ 

你看我像是怕的样子吗？~~瑟瑟发抖.jpg~~



全栈项目

Next.js 提供同构渲染

koa提供数据接口和服务端路由

Redis提供缓存服务（session储存）

直接生成：

```bash
npx create-next-app next-project  
```



集成antd design

1.首先根目录创建next.config.js文件next.config.js

2.安装[@zeit/next-css](https://github.com/zeit/next.js/tree/canary/examples/basic-css)和[@zeit/next-less](https://github.com/zeit/next-plugins/tree/master/packages/next-less)

