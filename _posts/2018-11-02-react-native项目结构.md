---
title: react-native项目结构
date: 2018-11-02
categories:
- react-native
tags:
- react-native
- Engineering
---

### react-native项目结构

```
|--android
|-- app               
|-- ios
|-- node_modules  
|-- index.js
|-- package.json
|-- ...
```

android：Android项目目录，包含了使用AndroidStudio开发项目的环境配置文件。

ios：iOS项目目录，包含了Xcode的环境配置文件。

app：react-native相关文件。

node_modules：基于node文件依赖系统产生的相关依赖。

index.js:：ios或android的项目运行时的入口文件。

package.json：项目基本信息以及依赖信息。

### app文件目录
```
|-- app               
    |-- actions
    |-- common
    |-- components
    |-- http
    |-- pages
    |-- reducers
    |-- store
```

actions、reducers、store：redux相关文件。

comon：存放一些公共文件，例如图片，样式主题等 。

components： 存放一些公共组件。

http：用于存放接口请求。

pages： 存放react-native构建页面。主程序的主要构成部分。

### common文件目录

```
|--common
    |-- images                
    |-- native-base-theme     
    |-- CommonColors.js  
    |-- CommonConfig.js
    |-- CommonImages.js
    |-- CommonUtil.js
    |-- CommonStyles.js
    |-- index.js
```

images：存放公用图片，icon，background等。

native-base-theme：native-base的主题样式（非必须）。

CommonColors.js ：公用的颜色，颜色统一管理。

CommonConfig.js：公用的全局配置，例如资源路径、接口地址、字符串常量等。

CommonImages.js：公用的图片路径管理。

CommonUtil.js：公用的一些工具类，例如设备屏幕的宽高、屏幕适配、判空处理、时间处理等。

CommonStyles.js：公用的CSS样式。

index.js：common文件引用的输出文件

### pages文件目录


```
|--pages
    |-- App.js                
    |-- Main.js     
    |-- Root.js  
    |-- Hot.js
    |-- Mine
        |-- Mine.js
        |-- style.js
        |-- Setting
    		|-- Setting.js
    		|-- style.js
    	|-- ... 
    |-- ...
```

App.js：路由注册，创建SwitchNavigator。

Main.js：路由注册，创建StackNavigator。

Root.js：文件入口。

Hot.js：热更新文件。

Mine：项目中‘“我的”的模块。

以下为业务页面：

Mine/Mine.js：“我的”模块的页面。

Mine/Mine.js：“我的”页面的CSS样式。

Mine/Setting： “我的”模块中的设置模块。

MIne/Setting/Setting.js：“我的”模块中的设置页面。

MIne/Setting/style.js：“我的”模块中设置页面的样式。

### redux相关的文件目录

#### actions文件目录


```
|--actions
    |-- type.js                
    |-- register.js     
    |-- user.js  
    |-- ...
```

#### reducers文件目录

```
|--reducers
    |-- index.js                
    |-- register.js    
    |-- user.js  
    |-- ...
```
#### store文件目录

```
|--store
    |-- index.js               
```



更多redux相关  [redux快速入门]()



——还会继续优化目录结构----