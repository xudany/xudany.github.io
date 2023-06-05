---
title: 关于React项目webpack打包性能的优化——hard-source-webpack-plugin篇
date: 2023-06-01
categories:
- webpack
tags:
- webpack
- react
- hard-source-webpack-plugin
- customize-cra
---



做webpack打包的性能优化，准备用hard-source-webpack-plugin，这是一个为模块提供中间缓存的插件，可以极大提高二次构建速度，如果在两次构建中都没有修改过某个文件，那么在第二次构建时，Webpack 可以直接从缓存中读取这个文件的编译结果，而不需要再次编译。好了，走起。



### 安装和配置

安装插件

```bash
npm install hard-source-webpack-plugin --save-dev
// 或者
yarn add hard-source-webpack-plugin --dev
```



配置文件

```js
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');

module.exports = {
  plugins: [
    new HardSourceWebpackPlugin(),
    // 其他插件
  ],
  // 其他配置
};
```



因为react项目里用的是`customize-cra`，所以是在 `config-overrides.js` 中配置：

```js
const { override, addWebpackPlugin } = require('customize-cra');
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');

module.exports = override(
  // 其他配置
  addWebpackPlugin(new HardSourceWebpackPlugin())
);
```



