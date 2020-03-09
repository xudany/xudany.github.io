---
title: react-router-dom的基本组件
date: 2019-06-08
categories:
- react
tags:
- react-router-dom
- Learing
---





# react-router-dom的基本组件

React Router中有三种类型的组件：路由器组件，路由匹配组件和导航组件。

在web应用中使用这些组件都需要从response -router-dom导入。

```js
import { BrowserRouter, Route, Link } from "react-router-dom"
```



## Routers（路由器组件）

每个React路由器应用程序的核心都应该是一个路由器组件。对于web项目，response -router-dom提供了`<BrowserRouter>`和`<HashRouter>`路由器。这两个路由器都将创建一个专门的history对象。一般来说，如果有响应请求的服务器，应该使用`<BrowserRouter>`；如果使用静态文件服务器，应该使用`<HashRouter>`。



```jsx
import { HashRouter } from "react-router-dom";
ReactDOM.render(
  <HashRouter>
    <App />
  </HashRouter>,
  holder
);
```

```jsx
import { BrowserRouter } from "react-router-dom";
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  holder
);
```



### `<HashRouter>`

官方文档：

>  A [`<Router>`](https://reacttraining.com/react-router/core/api/Router) that uses the hash portion of the URL (i.e. `window.location.hash`) to keep your UI in sync with the URL.
>
> **IMPORTANT NOTE:** Hash history does not support `location.key` or `location.state`. In previous versions we attempted to shim the behavior but there were edge-cases we couldn’t solve. Any code or plugin that needs this behavior won’t work. As this technique is only intended to support legacy browsers, we encourage you to configure your server to work with `<BrowserHistory>` instead.



简而言之，它使用URL的哈希部分（即`window.location.hash`）来保持页面的UI与URL同步。该技术只是用来支持旧版浏览器，因此更推荐大家使用 BrowserRouter。



### `<BrowserRouter>`

官方文档：

> A [`<Router>`](https://reacttraining.com/react-router/core/api/Router) that uses the HTML5 history API (`pushState`, `replaceState` and the `popstate` event) to keep your UI in sync with the URL.
>



一个`<Router>`，它使用HTML5 history API (`pushState`, `replaceState` and the `popstate` event)来保持UI与URL同步。




两者区别：`<HashRouter>`不支持location.key 、location.state



更多相关资料：

[React-router V4 中BrowserRouter和HashRouter的区别]([http://zhangdajia.com/2018/11/30/React-router-v4%E4%B8%ADBrowserRouter%E5%92%8CHashRouter%E7%9A%84%E5%8C%BA%E5%88%AB/](http://zhangdajia.com/2018/11/30/React-router-v4中BrowserRouter和HashRouter的区别/))

[react-router V4中三种router区别](https://www.zhihu.com/question/63662664)



## Route Matching（路由匹配）

有两个路由匹配组件：`<Route>`和`<Switch>`。

路由匹配是通过比较`<Route>`的路径和当前位置的路径名来完成的。当`<Route>`匹配到对应的路径时，它将渲染对应的内容；当匹配不到对应的路径时，它将渲染null。没有path属性的`<Route>`将总是被匹配到。

```jsx
import { Route, Switch } from "react-router-dom";

// 当前路径是 { pathname: '/about' }
<Route path='/about' component={About}/> // 渲染 <About/>
<Route path='/contact' component={Contact}/> // 渲染 null
<Route component={Always}/> // 没有path属性，渲染 <Always/>
```



您可以在基于位置呈现内容的任何地方使用`<Route>`。通常，多个`<Route>`并排列出是有意义的。`<Switch>`组件用于将多个`<Route>`进行分组。

```jsx
import { Route, Switch } from "react-router-dom";

<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
</Switch>
```



多个`<Route>`不一定需要用`<Switch>`进行分组，但是它非常有用。一个`<Switch>`将遍历它的所有子元素`<Route>`，并且只渲染与当前位置匹配的第一个元素。当多个路由的路径匹配相同的路径名时，当在路由之间进行动画转换时，以及当没有路由匹配当前位置时(这样就可以渲染一个“404”组件)，这都很有帮助。

```jsx
import { Route, Switch } from "react-router-dom";

<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
  {/* 当以上匹配项都不匹配时，将呈现<NoMatch> */}
  <Route component={NoMatch} />
</Switch>
```



【注意】一般根路由，父路由在匹配的时候都需要在`<Route>`加上exact属性，因为当`<Route>`添加exact属性后只有URL和该`<Route>`的path属性进行精确比对后完全相同该`<Route>`才会被渲染

详细原因参考：[react-router v4 之 啥是 `<Switch>`](https://www.jianshu.com/p/ed5e56994f13)



## Route Rendering Props(路由渲染属性)



对于如何渲染给定`<Route>`的组件，有三种方法可以选择：`component`、`render`、`children`。下面介绍两个常用的`component`和`render `。

`component`属性应该在有现有组件时使用(可以是React或者无状态的功能组件)。要呈现的组件或无状态功能组件。`render`属性接受内联函数，仅当必须将范围内变量传递给要渲染的组件时才应使用。您不应该使用带有内联函数的`component`的`props`属性来传递范围内变量，不然组件会 unmounts或者remounts。



```jsx
const Home = () => <div>Home</div>;

const App = () => {
  const someVariable = true;

  return (
    <Switch>
      {/* 可以这样使用 */}
      <Route exact path="/" component={Home} />
      <Route
        path="/about"
        render={props => <About {...props} extra={someVariable} />}
      />
      {/* 不能这样使用 */}
      <Route
        path="/contact"
        component={props => <Contact {...props} extra={someVariable} />}
      />
    </Switch>
  );
};
```



更多信息可看：

 [Route文档](https://reacttraining.com/react-router/web/api/Route)



## Navigation(导航)

React Router提供一个`<Link>`组件来在应用中创建链接。无论在何处使用<Link>，都会在应用的HTML中渲染一个`<a>`标签。

`<NavLink>`是一种特殊类型的`<Link>`，当它的`to`的属性匹配当前位置时，可以将自己设置为“active”。

任何时候您想强制重定向导航，都可以使用`<Redirect>`。当`<Redirect>`渲染时，它将重定向导航到`to`这个属性定义的路由。



```jsx
<Link to="/">Home</Link>
// <a href='/'>Home</a>

// location = { pathname: '/react' }
<NavLink to="/react" activeClassName="hurray">
  React
</NavLink>
// <a href='/react' className='hurray'>React</a>

<Redirect to="/login" />
```

