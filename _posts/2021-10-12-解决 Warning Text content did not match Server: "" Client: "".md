---
title: 解决 Warning Text content did not match Server: "" Client: ""
date: 2021-10-12
categories:
- next.js
tags:
- next.js
- localStorage
- react hook
- react 
- issue
---



在 Next.js 应用程序中使用了localStorage和react hook，在数据更新后重新刷新页面，就报错了：

> Warning: Text content did not match. Server: "2" Client: "3"

然后我的数据样式就有些异常了。



查了一下，说是当服务器返回与客户端不同的 html 结构时会发生此错误，这将迫使 React 重新渲染整个应用程序。我的情况是由于我使用了 localStorage ，localStorage 只能在客户端使用，在服务器端是没有 localStorag，所以加载的数据会不一致。

解决方法是用 useEffect 来同步更新 localStorage 对应的数据。



在查找的过程中，看到有些是想关掉这种警告的，解决方法是将 `suppressydrationwarning` 设置为 true。

```tsx
<MyComponent suppressHydrationWarning />
```
相关地址：[Is there any way to avoid "Text content did not match" warning in SSR with React?](https://stackoverflow.com/questions/53959948/is-there-any-way-to-avoid-text-content-did-not-match-warning-in-ssr-with-react)



参考资料：

[Next.js persistent state with React hooks and localStorage](https://stackoverflow.com/questions/59885433/next-js-persistent-state-with-react-hooks-and-localstorage

[Next js how to fetch localStorage data before client side rendering](https://stackoverflow.com/questions/68424114/next-js-how-to-fetch-localstorage-data-before-client-side-rendering))

[Next.js persistent state with React hooks and localStorage. How to make it work?](https://dev.to/jaklaudiusz/next-js-persistent-state-with-react-hooks-and-localstorage-how-to-make-it-work-3al6)