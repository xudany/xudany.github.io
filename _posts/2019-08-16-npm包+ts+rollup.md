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



## ts的配置文件

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



## rollup的配置文件

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



## babel的配置文件

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

