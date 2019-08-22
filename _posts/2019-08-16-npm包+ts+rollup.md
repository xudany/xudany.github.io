---
title: npm包+ts+rollup+babel
date: 2019-08-16
categories:
- npm
tags:
- npm
- ts
- rollup
---

npm包+ts+rollup+Babel 的一些相关配置



## ts的配置

需要安装的依赖

```bash
npm install --save typescript
```



tsconfig.json

```json
{
  "compilerOptions": {
    "outDir": "./dist",
    "module": "ES6",
    "target": "ES5",
    "lib": [
      "es6",
      "dom",
      "es7"
    ],
    "sourceMap": true,
    "jsx": "preserve",
    "moduleResolution": "node",
    "forceConsistentCasingInFileNames": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noImplicitAny": false,
    "importHelpers": true,
    "strictNullChecks": true,
    "allowSyntheticDefaultImports": true,
    "suppressImplicitAnyIndexErrors": true,
    "experimentalDecorators": true,
    "noUnusedLocals": false,
    "noUnusedParameters": false,
    "strictPropertyInitialization": false,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "strict": true,
    "declaration": true,
    "removeComments": true
  },
  "exclude": [
    "node_modules",
    "build",
    "dist"
  ]
}
```

相关链接

- [tsconfig.json配置的相关介绍](https://www.tslang.cn/docs/handbook/tsconfig-json.html)
- [ts编译选项](https://www.tslang.cn/docs/handbook/compiler-options.html)



## rollup的配置

需要安装的依赖

```bash
npm install --save-dev rollup rollup-plugin-node-resolve rollup-plugin-commonjs rollup-plugin-babel rollup-plugin-uglify rollup-plugin-json rollup-plugin-node-builtins rollup-plugin-node-globals
```



rollup.config.js

```javascript
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import babel from 'rollup-plugin-babel';
import {uglify} from 'rollup-plugin-uglify';
import json from 'rollup-plugin-json';
import builtins from 'rollup-plugin-node-builtins';
import globals from 'rollup-plugin-node-globals';
import pkg from './package.json';

const {version, name, author} = pkg;


const banner = `/*!
* ${name} v${version}
* (c) ${new Date().getFullYear()} ${author}
*/`;

export default [
  {
    input: './dist/index.js',
    plugins: [
      commonjs({
        // polyfill async/await
        'node_modules/@babel/runtime/helpers/asyncToGenerator.js': ['default']
      }),
      resolve({
        mainFields: false,
      }),
      babel({
        runtimeHelpers: true,
      }),
      globals(),
      builtins(),
      json()
    ],
    output: {
      file: `dist/npm-test-js-x.js`,
      format: 'umd',
      name: 'npmpackage-name',
      banner,
    }
  },
  {
    input: './dist/index.js',
    plugins: [
      commonjs({
        // polyfill async/await
        'node_modules/@babel/runtime/helpers/asyncToGenerator.js': ['default']
      }),
      resolve({
        mainFields: false,
      }),
      babel({
        runtimeHelpers: true,
      }),
      uglify(),
      globals(),
      builtins(),
      json()
    ],
    output: {
      file: `dist/npm-test-js-x.min.js`,
      format: 'umd',
      name: 'npm-test-js-x',
      banner,
    }
  },
  {
    input: './dist/index.js',
    plugins: [
      commonjs({
        // polyfill async/await
        'node_modules/@babel/runtime/helpers/asyncToGenerator.js': ['default']
      }),
      resolve({
        mainFields: false,
      }),
      babel({
        runtimeHelpers: true,
      }),
      globals(),
      builtins(),
      json()
    ],
    output: [
      {
        file: `dist/npm-test-js-x.es.js`,
        format: 'es',
        name: 'npm-test-js-x-es',
        banner,
      },
      {
        file: `dist/npm-test-js-x.cjs.js`,
        format: 'cjs',
        name: 'npm-test-js-x-cjs',
        banner,
      }
    ]
  },
];
```

参考链接：

- [如何搭建npm包](https://juejin.im/post/5c487f90f265da61715ea622)
- [如何通过 Rollup 来打包 JavaScript](https://juejin.im/entry/57edcefda22b9d005bb0d62c)



## babel的配置

需要安装的依赖

```bash
npm install --save-dev @babel/core @babel/plugin-transform-runtime @babel/preset-env babel-core babel-plugin-external-helpers babel-preset-latest babel-upgrade
```



.babelrc

```json
{
  "presets": [
    ["latest", {
      "es2015": {
        "modules": false
      }
    }]
  ],
  "plugins": ["external-helpers"]
}
```

参考链接：

- [为npm包提供多入口](https://xiaoiver.github.io/coding/2017/07/17/%E4%B8%BAnpm%E5%8C%85%E6%8F%90%E4%BE%9B%E5%A4%9A%E5%85%A5%E5%8F%A3.html)



## 配置过程中遇到的问题

### 1. TS2354: This syntax requires an imported helper but module 'tslib' cannot be found

报错内容：

```bash
 TS2354: This syntax requires an imported helper but module 'tslib' cannot be found
```

解决方法：

下载tslib

```bash
npm install tslib --save-dev
```



### 2. (plugin commonjs) TypeError [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string

报错内容：

```bash
[!] (plugin commonjs) TypeError [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string
TypeError [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string
```

解决方法：

rollup.config.js配置文件中的`input`字段输入有误，检查路径是否正确



### 3. [!] (plugin babel) SyntaxError

报错内容：

```bash
[!] (plugin babel) SyntaxError: E:/mproject/oauth-login-package/node_modules/axios/package.json: Unexpected token, expected ; (2:9)
node_modules\axios\package.json (2:9)
SyntaxError: E:/mproject/oauth-login-package/node_modules/axios/package.json: Unexpected token, expected ; (2:9)
  1 | {
> 2 |   "_from": "axios@^0.19.0",
    |          ^
  3 |   "_id": "axios@0.19.0",
  4 |   "_inBundle": false,
  5 |   "_integrity": "sha1-jgm/89kSLhM/e4EByPvdAO09Krg=",
```

解决方法：

修改rollup配置文件中的babel配置

rollup.config.js

```javascript
			babel({
				exclude: 'node_modules/**',
				runtimeHelpers: true,
			}),
```



### 4. [!] (plugin uglify) Error: Unexpected token: punc «,»

报错内容：

```bash
[!] (plugin uglify) Error: Unexpected token: punc «,»
SyntaxError: Unexpected token: punc «,»
```

解决方法：

这个错误定位后发现与rollup-plugin-uglify插件有关，rollup-plugin-uglify不能压缩es6的代码文件。rollup-plugin-uglify的[官方文档](https://github.com/TrySound/rollup-plugin-uglify)是说

> Note: uglify-js is able to transpile only es5 syntax. If you want to transpile es6+ syntax use terser instead
>

顺着这个思路有两种解决方法，一只要把es6的代码用babel转换成es5即可。二使用[rollup-plugin-terser](https://github.com/TrySound/rollup-plugin-terser)插件代替rollup-plugin-uglify

安装rollup-plugin-terser

```bash
yarn add rollup-plugin-terser --dev
```

使用rollup-plugin-terser

```javascript
import { rollup } from "rollup";
import { terser } from "rollup-plugin-terser";

rollup({
  input: "main.js",
  plugins: [terser()]
});
```



### 5.preferring built-in module 'http' over local alternative at 'http', pass 'preferBuiltins: false' to disable this behavior or 'preferBuiltins: true' to disable this warning

报错内容：

```bash
(!) Plugin node-resolve: preferring built-in module 'http' over local alternative at 'http', pass 'preferBuiltins: false' to disable this behavior or 'preferBuiltins: true' to disable this warning
(!) Plugin node-resolve: preferring built-in module 'https' over local alternative at 'https', pass 'preferBuiltins: false' to disable this behavior or 'preferBuiltins: true' to disable this warning
(!) Plugin node-resolve: preferring built-in module 'zlib' over local alternative at 'zlib', pass 'preferBuiltins: false' to disable this behavior or 'preferBuiltins: true' to disable this warning
```

解决方法：

设置rollup.config.js

```javascript
plugins: [
    ...
    resolve({
        preferBuiltins: true, //这一句是重点
		mainFields: ['browser']
    }),
    ...
],
```

或者可以这么做

```javascript
export default {
    ...
    external: ['http', 'https', 'zlib'],
    ...
}
```



### 6.(!) Circular dependency

报错内容：

```bash
(!) Circular dependency: node_modules\rollup-plugin-node-builtins\src\es6\readable-stream\duplex.js -> node_modules\rollup-plugin-node-builtins\src\es6\readable-stream\readable.js -> node_modules\rollup-plugin-node-builtins\src\es6\readable-stream\duplex.js
```

解决方法：

参考https://github.com/rollup/rollup/issues/1089

