---
title: qiankun的react子项目引入antd
date: 2021-12-02
categories:
- 微前端
tags:
- 微前端
- next.js
- qiankun
- antd
---



## qiankun的react子项目引入antd

这里默认已经引入了 `less` 的支持了，然后现在是配置 `antd` 的支持和一些全局变量的设置。关于引入 `less` 的支持可以看这里 [qiankun的react子项目引入less支持](https://xudany.github.io/%E5%BE%AE%E5%89%8D%E7%AB%AF/2021/12/01/qiankun%E7%9A%84react%E5%AD%90%E9%A1%B9%E7%9B%AE%E5%BC%95%E5%85%A5less%E6%94%AF%E6%8C%81/)

### 安装

需要用到 `customize-cra` 中的 `fixBabelImports`，还需要安装 `babel-plugin-import`

```bash
yarn add antd 
yarn add babel-plugin-import
// 如果没有装 customize-cra 的话需要
yarn add customize-cra -D
```



### 修改配置文件

修改配置文件 `config-overrides.js` ，是用在 `webpack.config.js` 使用 [less-loader](https://github.com/webpack-contrib/less-loader) 按需引入，下面是完整的配置文件，包括 `less` 的配置。

可以在 `modifyVars` 中对一些 antd 的样式变量进行设置。

```js
const { name } = require('./package.json');
const { override, fixBabelImports, addLessLoader, overrideDevServer, watchAll } = require(
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
    fixBabelImports('import', {
      libraryName: 'antd',
      libraryDirectory: 'es',
      style: true,
    }),
		/** 增加代码 **/
    
    addLessLoader({
      lessOptions: {
        javascriptEnabled: true,
        /** 增加代码 **/
        modifyVars: {
          'primary-color': '#304FFE',
          'success-color': '#00C853',
          'warning-color': '#FFA000',
          'error-color': '#F44336',
          '@ant-prefix': 'os-monitoring-platform',
        },
        /** 增加代码 **/
      },
    }),
  ),

  // devServer: (configFunction) => {
  //   return function(proxy, allowedHost) {
  //     const config = configFunction(proxy, allowedHost);
  //     config.open = false;
  //     config.hot = false;
  //     config.headers = {
  //       'Access-Control-Allow-Origin': '*',
  //     };
  //     return config;
  //   };
  // },

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



### antd样式前缀的设置

添加样式前缀是为了防止子项目样式全局污染，用 `@ant-prefix`是用来配置antd 样式的前缀，具体可以看下面的例子

App.tsx

```tsx
   <ConfigProvider prefixCls="custom_ant">
      <div className="App">
        <Button type="primary">CLICK ME</Button>
      </div>
    </ConfigProvider>
```

config-overrides.js

```js
    addLessLoader({
      lessOptions: {
        javascriptEnabled: true,
        modifyVars: {
          //添加
          '@ant-prefix': 'custom_ant',
        },
      },
    }),
```



### **注意**

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

[qiankun的react子项目引入less支持](https://xudany.github.io/%E5%BE%AE%E5%89%8D%E7%AB%AF/2021/12/01/qiankun%E7%9A%84react%E5%AD%90%E9%A1%B9%E7%9B%AE%E5%BC%95%E5%85%A5less%E6%94%AF%E6%8C%81/)



### 参考链接

[setting prefixCls using ConfigProvider breaks styling ](https://github.com/ant-design/ant-design/issues/27245)

[antd定制主题](https://ant.design/docs/react/customize-theme-cn)