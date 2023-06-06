---
title: 关于React项目webpack打包性能的优化——happypack篇
date: 2023-06-01
categories:
- webpack
tags:
- webpack
- react
- happypack
- customize-cra
---



做webpack打包的性能优化，准备用happypack，这个插件可以将Babel等加载器的工作分配到多个子进程中，这样可以加快构建速度。好了，走起。



### 安装和配置

安装插件

```bash
npm install happypack --save-dev
// 或者
yarn add happypack --dev
```



配置文件

```js
const HappyPack = require('happypack');
const os = require('os');
const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });

module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.js$/,
        // 排除 node_modules 目录下的文件
        exclude: /node_modules/,
        use: 'happypack/loader?id=happyBabel',
      },
    ],
  },
  plugins: [
    new HappyPack({
       // id 标识符，要和 rules 中指定的 id 对应起来
      id: 'happyBabel',
      // 需要使用的 loader，用法和 rules 中 Loader 配置一样
      // 可以直接是字符串，也可以是对象形式 loaders: ['babel-loader?cacheDirectory']
      loaders: [
        {
          loader: 'babel-loader',
          options: {
            cacheDirectory: true,
          },
        },
      ],
      threadPool: happyThreadPool,
    }),
  ],
};
```

上面的HappyPack 创建了一个名为 `happyBabel` 的 HappyPack 实例。当 webpack 遇到一个匹配规则的模块（`.js` 文件，不包括 `node_modules` 目录下的文件）时，它会使用 HappyPack 来处理这个模块。在 HappyPack 的配置中，使用 `babel-loader` 来转译 JavaScript 文件，这将在一个单独的线程池中进行，以提高性能。



在我的项目里，是在 `config-overrides.js` 中配置：

```js
const { override, babelInclude } = require('customize-cra');
const path = require('path');
const HappyPack = require('happypack');
const os = require('os');
const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });

const HappyPackPlugin = new HappyPack({
    id: "babel",
    loaders: ["babel-loader?cacheDirectory"],
    threadPool: happyThreadPool
});

module.exports = override(
  // 其他配置...
  addWebpackPlugin(HappyPackPlugin),
);

```



### 参考资料
[使用 happypack 提升 Webpack 项目构建速度](https://juejin.cn/post/6844903780337582088)