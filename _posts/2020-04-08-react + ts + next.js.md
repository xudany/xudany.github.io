---
title: react + ts + next.js
date: 2020-04-08
categories:
- react
tags:
- react
- ts
- next.js
---



该来的总会来，后端渲染学习走起。啦啦啦(～￣▽￣)～ 

你看我像是怕的样子吗？~~瑟瑟发抖.jpg~~



全栈项目

Next.js 提供同构渲染

koa提供数据接口和服务端路由

Redis提供缓存服务（session储存）



### 脚手架直接生成

```bash
npx create-next-app next-project  
```



### [集成antd design](https://github.com/zeit/next.js/tree/canary/examples/with-ant-design)

1. 安装[@zeit/next-css](https://github.com/zeit/next.js/tree/canary/examples/basic-css)

   ```bash
   npm install @zeit/next-css --save
   npm install @zeit/next-less --save
   ```

   

2. 在page文件夹下创建_app.js

   _app.js

   ```js
   import 'antd/dist/antd.css'
   
   export default function MyApp({ Component, pageProps }) {
     return <Component {...pageProps} />
   }
   ```
   然后antd就可以用啦~



### [集成typescript](https://github.com/zeit/next.js/tree/canary/examples/with-typescript)

1. 安装typescript

   ```bash
   npm install --save-dev typescript
   ```

2. 为了启用TypeScript的功能，为React和Node安装类型声明

   ```bash
   npm install --save-dev @types/react @types/react-dom @types/node
   ```
   
3. 根目录下增加ts配置文件tsconfig.json

   tsconfig.json
   ```json
   {
     "compilerOptions": {
       "allowJs": true,
       "alwaysStrict": true,
       "esModuleInterop": true,
       "forceConsistentCasingInFileNames": true,
       "isolatedModules": true,
       "jsx": "preserve",
       "lib": [
         "dom",
         "es2017"
       ],
       "module": "esnext",
       "moduleResolution": "node",
       "noEmit": true,
       "noFallthroughCasesInSwitch": true,
       "noUnusedLocals": true,
       "noUnusedParameters": true,
       "resolveJsonModule": true,
       "skipLibCheck": true,
       "strict": true,
       "target": "esnext"
     },
     "exclude": [
       "node_modules"
     ],
     "include": [
       "**/*.ts",
       "**/*.tsx"
     ]
   }
   ```

4. 更改 index.js 和 _app.js 文件名为 index.tsx 和 _app.tsx

   然后_app.tsx应该就会报错了，错误信息如下：

   ```bash
   Binding element 'Component' implicitly has an 'any' type.
   ```
   修改_app.tsx里面的内容
   ```js
   import 'antd/dist/antd.css'
   
   export default function MyApp({Component, pageProps}: any) {
       return <Component {...pageProps} />
   }
   ```



### 集成less

1. 安装[@zeit/next-less](https://github.com/zeit/next-plugins/tree/master/packages/next-less)

```bash
npm install --save @zeit/next-less
```

2. 根目录创建配置文件next.config.js

   ```js
   // next.config.js
   const withLess = require('@zeit/next-less')
   module.exports = withLess({
     /* config options here */
   })
   ```

然后就成功报错_(:з」∠)_ 生气

```bash
[ error ] ./node_modules/antd/dist/antd.css 15:5
Module parse failed: Unexpected token (15:5)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| /* stylelint-disable at-rule-no-unknown */
| html,
> body {
|   width: 100%;
|   height: 100%;
...
```

 看了错误，css不支持啊，修改一下配置文件next.config.js

```bash
// next.config.js
const withCss = require('@zeit/next-css');
const withLess = require('@zeit/next-less');
module.exports = withCss(withLess({}));
```

啊哈，又报错。

```bash
Error: Cannot find module 'less'
```

不过这个好解决，安装less依赖就可以啦~
```bash
npm install less --save
```



### [ant按需加载 + less](https://github.com/zeit/next.js/tree/canary/examples/with-ant-design-less)

1. 根目录建配置文件.babelrc

   .babelrc
   
   ```bash
   {
     "presets": ["next/babel"],
     "plugins": [
       [
         "import", {
         "libraryName": "antd",
         "style": true
       }
       ]
     ]
   }
   ```
   
2. 安装`babel-plugin-import`

   ```bash
   npm install babel-plugin-import --save
   ```


3. 更改配置文件next.config.js

  next.config.js

  ```js
  /* eslint-disable */
  const withLess = require('@zeit/next-less')
  const lessToJS = require('less-vars-to-js')
  const fs = require('fs')
  const path = require('path')
  
  // Where your antd-custom.less file lives
  const themeVariables = lessToJS(
      fs.readFileSync(path.resolve(__dirname, './assets/antd-custom.less'), 'utf8')
  )
  
  module.exports = withLess({
    lessLoaderOptions: {
      javascriptEnabled: true,
      modifyVars: themeVariables, // make your antd custom effective
    },
    webpack: (config, { isServer }) => {
      if (isServer) {
        const antStyles = /antd\/.*?\/style.*?/
        const origExternals = [...config.externals]
        config.externals = [
          (context, request, callback) => {
            if (request.match(antStyles)) return callback()
            if (typeof origExternals[0] === 'function') {
              origExternals[0](context, request, callback)
            } else {
              callback()
            }
          },
          ...(typeof origExternals[0] === 'function' ? [] : origExternals),
        ]
  
        config.module.rules.unshift({
          test: antStyles,
          use: 'null-loader',
        })
      }
      return config
    },
  })
  ```

4. 安装相关依赖
  ```bash
  npm install less --save
  npm install less-vars-to-js --save 
  npm install null-loader --save
  ```

5. 增加 assets/antd-custom.less 配置ant的主题
	```css
	@primary-color: #52c41a;
	
	@layout-header-height: 40px;
	@border-radius-base: 2px;
	
	```
	
6. 更改_app.tsx
	```js
	export default function MyApp({Component, pageProps}: any) {
	    return <Component {...pageProps} />
	}
	```

















