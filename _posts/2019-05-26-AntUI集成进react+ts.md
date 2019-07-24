---
title: AntUI集成进react+ts
date: 2019-05-26
categories:
- AntUI
tags:
- react
- ts
- Learning
---

## AntUI集成进react+ts

1. 添加老版antd (3.16.3)

```bash
npm install antd --save
```

按需引入组件

```bash
npm install react-app-rewired --save-dev
```

修改package.json的配置

```json
/* package.json */
"scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start --scripts-version react-scripts-ts",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build --scripts-version react-scripts-ts",
-   "test": "react-scripts test --env=jsdom",
+   "test": "react-app-rewired test --env=jsdom --scripts-version react-scripts-ts",
}
```

然后在项目根目录创建一个 `config-overrides.js` 用于修改默认配置。

```js
module.exports = function override(config, env) {
  // do stuff with the webpack config...
  return config;
};
```

使用 ts-import-plugin

​       [ts-import-plugin](https://github.com/Brooooooklyn/ts-import-plugin) 是一个用于按需加载组件代码和样式的 TypeScript 插件（[原理](http://beta.ant.design/docs/react/getting-started-cn#按需加载)），现在我们尝试安装它并修改 `config-overrides.js` 文件。

```bash
  $npm install ts-import-plugin --save-dev
```

```javascript
  /* config-overrides.js */
  const tsImportPluginFactory = require('ts-import-plugin')
  const { getLoader } = require("react-app-rewired");

  module.exports = function override(config, env) {
    const tsLoader = getLoader(
      config.module.rules,
      rule =>
        rule.loader &&
        typeof rule.loader === 'string' &&
        rule.loader.includes('ts-loader')
    );

    tsLoader.options = {
      getCustomTransformers: () => ({
        before: [ tsImportPluginFactory({
          libraryName: 'antd',
          libraryDirectory: 'es',
          style: 'css',
        }) ]
      })
    };

    return config;
  }
```

  然后移除前面在 `src/App.css` 里全量添加的 `@import '~antd/dist/antd.css';` 样式代码，并且按下面的格式引入模块。

```javascript
  import * as React from 'react';
  import { Button } from 'antd';
  import './App.css';

  class App extends React.Component {
    render() {
      return (
        <div className="App">
          <Button type="primary">Button</Button>
        </div>
      );
    }
  }

  export default App;
```



报错：

```bash
Cannt find module react-scripts-ts/config/webpack.config
```

解决：

使用`yarn add react-app-rewired@1.6.2`或`npm install react-app-rewired@1.6.2`退回到react-app-rewired的1.x版本的最后一个版本

原因：

> This has been caused by recent changes to CRA (2.1.2) where they merged the `webpack.config.dev.js` and `webpack.config.prod.js` into a single file `webpack.config.js`. See [#343](https://github.com/timarney/react-app-rewired/issues/343) and [#345](https://github.com/timarney/react-app-rewired/issues/345) for more details on the change.
>
> The adjustment to paths made in `react-app-rewired` in order to continue to be able to be used with CRA looks for a `react-scripts` version number greater than or equal to 2.1.2. The `react-scripts-ts`version number has been higher than that for the last ~18 months, so `react-app-rewired` is treating it according to the merged webpack config instead of the split one.
>
> To solve it, you'll need to install an older version of `react-app-rewired` that doesn't have this change. I believe the last version of `react-app-rewired` that will be compatible with `react-scripts-ts` is version `1.6.2`. The versions for 2.x have the breaking change to support CRA 2.1.2 and beyond that is misfiring on `react-scripts-ts`.
>
> Use `yarn add react-app-rewired@1.6.2` or `npm install react-app-rewired@1.6.2` to step back to the last of the 1.x versions of react-app-rewired - it will install the older version and lock the version number to exactly that version so that it doesn't get upgraded to a newer version accidentally in future.

参考： <https://github.com/timarney/react-app-rewired/issues/358>

报错：

```bash
The key 'libraryDirectory' is not sorted alphabetically
```

解决：

 config-overrides.js 文件里交换下libraryName和libraryDirectory的位置。

参考： <https://github.com/ant-design/ant-design/issues/10533>

报错：

```bash
interface name must not have an "I" prefix
```

解决：

根据参考网址添加

```json
"interface-name" : [true, "never-prefix"]
```

对我的项目而言无效，需要修改成

```json
"interface-name": false
```

即是在tslint.json的rules中添加

```json
{
  "extends": [
    "tslint:recommended",
    "tslint-react",
    "tslint-config-prettier"
  ],
  "linterOptions": {
    "exclude": [
      "config/**/*.js",
      "node_modules/**/*.ts",
      "coverage/lcov-report/*.js"
    ]
  },
  "rules": {
    // 添加这一句
    "interface-name": false,
    ...
  }
}

```

参考： <https://github.com/palantir/tslint/issues/3265>



安装新版 ant3.20.3

```bash
npm install antd --save
```

全局引用

修改 `src/App.tsx`，引入 antd 的按钮组件。

```jsx
import React, { Component } from 'react';
import Button from 'antd/es/button';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <Button type="primary">Button</Button>
      </div>
    );
  }
}

export default App;
```

修改 `src/App.css` 引入 antd 的样式。

```css
@import '~antd/dist/antd.css';

.App {
  text-align: center;
}

...
```

重新启动 `yarn start`，现在你应该能看到页面上已经有了 antd 的蓝色按钮组件，接下来就可以继续选用其他组件开发应用了。其他开发流程你可以参考 create-react-app 的[官方文档](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md)。



高级配置，按需引入

此时我们需要对 create-react-app 的默认配置进行自定义，这里我们使用 [react-app-rewired](https://github.com/timarney/react-app-rewired) （一个对 create-react-app 进行自定义配置的社区解决方案）。

引入 react-app-rewired 并修改 package.json 里的启动配置。由于新的 [react-app-rewired@2.x](https://github.com/timarney/react-app-rewired#alternatives) 版本的关系，你还需要安装 [customize-cra](https://github.com/arackaf/customize-cra)。

```bash
npm install react-app-rewired customize-cra --save
```

```diff
/* package.json */
"scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build",
-   "test": "react-scripts test",
+   "test": "react-app-rewired test",
}
```

然后在项目根目录创建一个 `config-overrides.js` 用于修改默认配置。

```js
module.exports = function override(config, env) {
  // do stuff with the webpack config...
  return config;
};
```

使用 babel-plugin-import

```bash
npm install babel-plugin-import --save
```

[babel-plugin-import](https://github.com/ant-design/babel-plugin-import) 是一个用于按需加载组件代码和样式的 babel 插件（[原理](https://ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD)），现在我们尝试安装它并修改 `config-overrides.js` 文件。

```javascript
const { override, fixBabelImports } = require('customize-cra');

module.exports = override(
  fixBabelImports('import', {
    libraryName: 'antd',
    libraryDirectory: 'es',
    style: 'css',
  }),
);
```

然后移除前面在 `src/App.css` 里全量添加的 `@import '~antd/dist/antd.css';` 样式代码，并且按下面的格式引入模块。

```diff
  // src/App.tsx
  import React, { Component } from 'react';
- import Button from 'antd/es/button';
+ import { Button } from 'antd';
  import './App.css';

  class App extends Component {
    render() {
      return (
        <div className="App">
          <Button type="primary">Button</Button>
        </div>
      );
    }
  }

  export default App;
```

最后重启 `yarn start` 访问页面，antd 组件的 js 和 css 代码都会按需加载，你在控制台也不会看到这样的[警告信息](https://zos.alipayobjects.com/rmsportal/vgcHJRVZFmPjAawwVoXK.png)。关于按需加载的原理和其他方式可以阅读[这里](https://ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD)。



自定义主题
需要安装less

```bash
npm install less less-loader --save
```



按照 [配置主题](https://ant.design/docs/react/customize-theme-cn) 的要求，自定义主题需要用到 less 变量覆盖功能。我们可以引入 `customize-cra` 中提供的 less 相关的函数 [addLessLoader](https://github.com/arackaf/customize-cra#addlessloaderloaderoptions) 来帮助加载 less 样式，同时修改 `config-overrides.js` 文件如下。

```diff
- const { override, fixBabelImports } = require('customize-cra');
+ const { override, fixBabelImports, addLessLoader } = require('customize-cra');

module.exports = override(
  fixBabelImports('import', {
    libraryName: 'antd',
    libraryDirectory: 'es',
-   style: 'css',
+   style: true,
  }),
+ addLessLoader({
+   javascriptEnabled: true,
+   modifyVars: { '@primary-color': '#1DA57A' },
+ }),
);
```

这里利用了 [less-loader](https://github.com/webpack/less-loader#less-options) 的 `modifyVars` 来进行主题配置，变量和其他配置方式可以参考 [配置主题](https://ant.design/docs/react/customize-theme-cn) 文档。

修改后重启 `yarn start`，如果看到一个绿色的按钮就说明配置成功了。