---
title: markdown自动生成HTML文件
date: 2018-05-25 
categories:
- Markdown
tags:
- Markdown
- node.js
- html
- marked.js
---
# markdown自动生成HTML文件

主要是用marked.js来实现markdown文本的解析

### 1.安装marked.js

```
npm install marked --save 
```

### 2.需要转换成HTML的markdown文件

```
<a name="1.1.4"></a>
## 1.1.4 (2018-05-15)


### Bug Fixes

* 修改了登录界面的样式


### Features

* 增加了访客模式的功能（需要验证码登录）



<a name="1.1.3"></a>
## 1.1.3 (2018-05-10)


### Bug Fixes

* 调整角色栏的样式为自适应


### Features

* 增加menu根据权限显示的功能
* 角色栏设置成可配置项目
* 设置角色栏为不可选
* 增加登录页面“记住我”的功能



<a name="1.1.2"></a>
## 1.1.2 (2018-05-09)


### Bug Fixes

* 调整页面的样式：

  1. 调整swiper-slide整版显示数目
  2. 调整menu和swiper 为自适应
  
* 解决缩小页面无法正常显示的bug


### Features

* 增加支持4K的标识
* 增加权限功能



<a name="1.1.0"></a>
## 1.1.0 (2018-05-07)


### Bug Fixes

* 安装Bable Polyfill，使其兼容IE


### Features

* 增加copyright
* 优化页面部分样式
* 增加swiper的功能
```

### 3.用于生成HTML文件的模板

模板里的`@markdown`是用来替换的，可以在`<head>`中引入css样式

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ChangeLog</title>
    <link href='./css/changeLog.css' rel='stylesheet' type='text/css' />
</head>

<body class='typora-export os-windows'>
<div id='write'  class = 'is-node'>
    @markdown
</div>
</body>
</html>
```

### 4.markdown自动生成HTML文件的脚本

调用`marke("md")`就能解析markdown文本， 用`fs.watchFile`实时监听，如果markdown文件被修改，可以实时更新HTML里面的内容，生成HTML文件的路径和名字都可自定义

```
const fs = require('fs');
const marked = require('marked');
var path = require('path');

let fileArray = path.resolve(__dirname, '../CHANGELOG.md');

function changeLog(filePath) {
   
   fs.watchFile(path.resolve(__dirname, '../CHANGELOG.md'), (curr, prev)=> {
      // 读取HTML模板文件
      fs.readFile(path.resolve(__dirname, '../static/changeLog/template.html'), 'utf8', (err, template) => {
         if (err) {
            throw err;
         } else {
            fs.readFile(filePath, 'utf8', (err, markContent) => {
               if (err) {
                  throw err;
               } else {
                  let htmlStr = marked(markContent.toString());
                  template = template.replace('@markdown', htmlStr);
                  //输出HTML文件
                  fs.writeFile(path.resolve(__dirname, '../static/changeLog/CHANGELOG.html'), template, err => {
                     if (err) {
                        throw err;
                     } else {
                        console.log("success");
                     }
                  })
               }
            })
         }
      })
   })
}


(function changeFile() {
   changeLog(fileArray);
})();
```