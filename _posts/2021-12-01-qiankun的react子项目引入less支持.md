---
title: qiankun的react子项目引入less支持
date: 2021-12-01
categories:
- 微前端
tags:
- 微前端
- next.js
- qiankun
- less
---



## qiankun的react子项目引入less支持

因为 qiankun 框架的 react 子项目本身就需要使用 `react-app-rewired` 对 `webpack` 的一些配置进行重写，所以配合使用 `customize-cra` 就可以做到对 less 的支持。



### 安装相关的依赖

首先肯定是国际惯例一通下载，下载 `less`、`less-loader`、`customize-cra`，建议使用以下版本，不然会出现一些奇怪的问题，比如 [addLessLoader issue when using less-loader](https://github.com/arackaf/customize-cra/issues/241)

```bash
yarn add less@3.11.3 less-loader@6.1.0 -D
```

```bash
yarn add customize-cra -D
```



### 修改配置文件

修改配置文件 `config-overrides.js` 

```js
const { name } = require('./package.json');
const { override, addLessLoader, overrideDevServer, watchAll } = require(
  'customize-cra');

const { NODE_ENV, REACT_APP_ROUTER_PATH } = process.env;

module.exports = {
  webpack: override(
    (config) => {
      config.output.library = `${name}-[name]`;
      config.output.libraryTarget = 'umd';
      config.output.jsonpFunction = `webpackJsonp_${name}`;
      config.output.globalObject = 'window';
      config.output.publicPath = NODE_ENV === 'production' ? `${REACT_APP_ROUTER_PATH}/` : '';
      return config;
    },

    /** 增加代码 **/
    addLessLoader({
      lessOptions: {
        javascriptEnabled: true,
      },
    }),
    /** 增加代码 **/
  ),

  devServer: overrideDevServer(
    (config) => {
      config.open = false;
      config.hot = false;
      config.headers = { 'Access-Control-Allow-Origin': '*' };
      return config;
    },
    watchAll(),
  ),

};

```

顺便一提，`customize-cra` 中的 `addWebpackAlias` 模块可以用来添加别名，设置相对路径。

```js
const path = require('path');
module.exports = {
  webpack: override(    
    addWebpackAlias({      
        '@': path.resolve(__dirname, "src"),  
    }))
}
```



**注意**

如果上述改完之后不生效，看看是不是使用了 **[rescripts](https://github.com/harrysolovay/rescripts)** ，如果是的话，把 package.json 中的 `rescripts` 改为 `react-app-rewired`就可以了。

package.json

```diff
{
  "name": "built-with-rescripts",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react": "^16.6.1",
    "react-dom": "^16.6.1",
    "react-scripts": "2.1.1"
  }
  "devDependencies": {
    "@rescripts/cli": "^0.0.11",
    "@rescripts/rescript-env": "^0.0.10"
  }
  "scripts": {
-   "start": "rescripts start",
+   "start": "react-app-rewired start",
-   "build": "rescripts build",
+   "build": "react-app-rewired build",
-   "test": "rescripts test",
+   "test": "react-app-rewired test",
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": [
    ">0.2%",
    "not dead",
    "not ie <= 11",
    "not op_mini all"
  ]
}
```



### 一些相关链接

[next.js + qiankun 项目实践](https://xudany.github.io/%E5%BE%AE%E5%89%8D%E7%AB%AF/2021/10/19/next.js-+-qiankun-%E9%A1%B9%E7%9B%AE%E5%AE%9E%E8%B7%B5/)

[qiankun的react子项目引入antd]()



### 参考资料

[customize-cra配置config-overrides.js支持less和qiankun](https://www.codeleading.com/article/58255896466/)