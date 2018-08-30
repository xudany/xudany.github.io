---
title: 2018-08-25-Change Log小工具使用指南
date: 2018-08-28
categories:
- Change Log commander inquirer node
tags:
- Change Log commander inquirer node
---



## 安装

把文件夹bin置于项目根目录

把文件夹ChangeLog置于根目录文件static下 即（static/ChangeLog）

在package.json中添加配置

```
    "bin": {
        "cl": "./bin/changelog.js"
    }
```

运行命令

```
npm link
```



## 生成

使用前需要安装相关依赖，运行命令

```
cl i
```

两种方法实现创建changeLog

####1.  使用配置文件

   在文件bin中的config.json中填写配置项

| 字段名             | 参数 （类型） | 描述                                                  |
| ------------------ | ------------- | ----------------------------------------------------- |
| name               | string        | 项目名称                                              |
| description        | string        | 项目描述                                              |
| first*             | boolean       | 是否第一次生成changeLog                               |
| initVersion        | string        | 第一次生成changeLog的版本号（first为true时，必填）    |
| baseVersion*       | string        | 根据哪一个文件的版本号来生成changeLog                 |
| changeVersionPath* | Array[string  | 需要更改版本号的文件路径（相对路径）（相对于bin文件） |

   *：为必填项

   例子：

   ```
   {
     "name": "config",
     "description": "配置项",
     "first": false,
     "initVersion": "1.0.0",
     "baseVersion": "../package.json",
     "changeVersionPath": [
       "../package.json",
       "../lib/package.json",
       "package.json"
     ]
   }
   ```

   

   运行以下命令来自动化创建changLog

   ```
   cl s
   ```

   根据提示输入版本号完成创建。

   

#### 2. 不使用配置文件

   运行以下命令来自动化创建changlog

   ```
   cl a
   ```

   之后会提问你是否是第一次生成changeLog

   ```
   ? 是否是第一次生成changeLog？ (Y/n)
   ```

   如果是，则输入y，之后会自动化生成一系列配置文件

   

   如果不是，则输入n

   之后你会被提示输入需要更新版本号的json文件路径

   ```
   ? 请输入需要更新版本号的json文件路径(相对路径)：
   ```

   这个路径需要输入相对路径，比如根目录的package.json则输入路径为 `../package.json`

   ```
   ? 请输入需要更新版本号的json文件路径(相对路径)： ../package.json
   ```

   默认情况下，会自动更新根目录的packag.json中的版本号，所以此处用于其他文件所需要的版本号更新

   之后会给出老版本号，提示输入新的版本号

   ```
   i 文件中的版本号为1.1.2
   ? 请输入新的版本号：
   ```

   输入正确的版本号后，会自动化生成一系列配置文件以及changeLog

   

## 使用

   生成的更新日志HTML网页会在项目路径`static/ChangeLog`下，为CHANGELOG.html

   可以在项目中使用a标签自行引入

   

   如果需要在页面中动态使用版本号，则用以下方法引入

   <script>中的代码

   ```
   created() {
      this.version = document.getElementsByName('version')[0].content;
   }
   ```

   ```
   data() {
      return {
         version: "1.0.0"
      }
   },
   ```

   <template>中的代码

   ```
   <div>{{version}}</div>
   ```

   

## 其他命令

可以通过运行以下命令进行探索： 

```
cl --help
```



  Options:

    -V, --version                  output the version number
    -h, --help                     output usage information


  Commands:

    version|v [options]            输出版本号
    install|i [options]            安装相关依赖
    createVersion|cev [options]    生成版本号
    createChangeLog|ccl [options]  自动生成changeLog.md文件
    convert|cvh [options]          markdown转成html
    auto|a [options]               自动化生成全部设置
    simple|s [options]             需要配置文件的简单初始化



## 其他事项

如果需要修改生成的css样式，则在项目路径`static/ChangeLog/css`下的changeLog.css文件进行修改

如果需要修改头部<title>等HTML相关，则在项目路径`static/ChangeLog`下的模板文件template.html进行修改

注意： 不可以改动此部分，会影响HTML模板的生成！

```
<div id='write'>
    @markdown
</div>
```