---
title: Can't import the named export 'SetArray' from non EcmaScript module
date: 2023-09-11
categories:
- webpack 
tags:
- webpack
- issue
- react-scripts
- customize-cra
---


问题：Can't import the named export 'SetArray' from non EcmaScript module (only default export is available)

原因：跟依赖的版本号或者 webpack 打包配置有关

解决方法：

1. 如果包中有使用`react-scripts`，确保其版本号为"^5.0.1"以上，然后重新 install。
2. 如果`react-scripts`版本是3.x又不想升级版本，需要更改`webpack.config.js`配置文件，使其支持`.mjs`的格式，如下：
webpack.config.js
```js
const config = {
  mode: 'production', // "production" | "development" | "none"
  resolve: {
    extensions: ['*', '.mjs', '.js', '.json']
  },
  module: {
    rules: [
      {
        test: /\.mjs$/,
        include: /node_modules/,
        type: 'javascript/auto'
      }
    ]
  }
}

module.exports = config
```
因为用的是`customize-cra`，所以更改根目录`config-overrides.js`文件为：
```js
const addMJSRule = (config) => {
    // 找到JavaScript和MJS文件的规则
    const jsRule = config.module.rules.find(rule => rule.test && rule.test.test(".js"));
    if (jsRule) {
        const mjsRule = {
            test: /\.mjs$/,
            include: /node_modules/,
            type: "javascript/auto"
        };

        // 将MJS规则添加到配置中
        config.module.rules.push(mjsRule);

        // 在resolve.extensions中添加.mjs
        config.resolve.extensions.push(".mjs");
    }

    return config;
};

module.exports = {
    webpack: override(
    		...
        addMJSRule,
        ...
    ),
};

```

参考资料：

[ Can't import the named export 'SetArray' from non EcmaScript module](https://github.com/babel/babel/issues/14550)

[Can't import the named export XXXX from non EcmaScript ...](https://stackoverflow.com/questions/69343038/cant-import-the-named-export-xxxx-from-non-ecmascript-module-only-default-expo)

