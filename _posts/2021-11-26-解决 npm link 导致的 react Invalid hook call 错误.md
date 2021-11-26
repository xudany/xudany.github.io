---
title: 解决 npm link 导致的 react Invalid hook call 错误
date: 2021-11-26
categories:
- npm
tags:
- npm
- npm link
- react
- issue
---

随手记。

在调试本地组件库代码时，link到主项目时报错了非法Hook调用：

```bash
Invalid hook call. Hooks can only be called inside of the body of a function component.
```

官方文档报错原因：

>There are three common reasons you might be seeing it:
>You might have mismatching versions of React and React DOM.
>You might be breaking the Rules of Hooks.
>You might have more than one copy of React in the same app.

总而言之，在 npm link 这个使用场景下就是存在组件库里面的react和引用组件库项目里的react冲突。

搜索了一下，找到了一些解决方法，就记一下吧。

1. 将组件库依赖的react指向主项目node_modules中安装的react。

  ```bash
  cd [PACKAGE]
  npm link [PROJECT]/node_modules/react
  ```

  然后重启主项目。


2. 在主项目（不是被link的npm包）中使用alias指定加载的react依赖

  webpack.config.js
  ```js
  resolve: {
    alias: {
      'react': path.resolve(__dirname, 'node_modules/react')
    }
  }
  ```

  我的主项目是用react-app-rewired这个插件，所以配置如下：

  config-overrides.js
  ```js
  addWebpackAlias({
    ["react"]: path.resolve(__dirname, "node_modules/react"),
  }),
  ```


3. 采用更好的调试方式：yalc

  ```bash
  npm i yalc -g
  yalc publish // 在对应的 npm 包中发布

  yalc link // 在对应的项目中 link 对应的包
  ```

  附上一个官网 [yalc](https://github.com/wclr/yalc)



参考资料：

[Invalid Hook Call Warning](https://reactjs.org/warnings/invalid-hook-call-warning.html)

[解决npm link本地代码后 react库存在多份实例的问题](https://juejin.cn/post/6922687641485836301)

[居然有比 npm link 更好的调试？](https://jishuin.proginn.com/p/763bfbd5b451)