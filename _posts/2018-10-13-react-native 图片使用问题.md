---
title: react-native 图片使用问题
date: 2018-10-13
categories:
- react-native 
tags:
- react-native
- issue
---

## 静态图片资源

要往 App 中添加一个静态图片，只需把图片文件放在代码文件夹中某处，然后这样去引用它：

```javascript
<Image source={require("./my-icon.png")} />
```

注意：图片文件的查找会和 JS 模块的查找方式一样。Packager 可以通过图片命名，根据不同条件选择不同的图片文件。

根据平台而选择不同的图片，可以这样命名

```
·
├── my-icon.ios.png
└── my-icon.android.png
```

根据不同的屏幕精度提供不同的图片，可以这样命名

```
·
├── my-icon@1x.png
├── my-icon@2x.png
└── my-icon@3x.png
```

注意：为了使新的图片资源机制正常工作，require 中的图片名字必须是一个静态字符串（不能使用变量！因为 require 是在编译时期执行，而非运行时期执行！）。

```javascript
// 正确
<Image source={require("./my-icon.png")} />;

// 错误
var icon = this.props.active ? "my-icon-active" : "my-icon-inactive";
<Image source={require("./" + icon + ".png")} />;

// 正确
var icon = this.props.active
  ? require("./my-icon-active.png")
  : require("./my-icon-inactive.png");
<Image source={icon} />;
```

请注意：通过这种方式引用的图片资源包含图片的尺寸（宽度，高度）信息，如果需要动态缩放图片（例如，通过 flex），需要在 style 属性设置`{ width: null, height: null }`。



## 网络图片

很多要在 App 中显示的图片并不能在编译的时候获得，又或者有时候需要动态载入来减少打包后的二进制文件的大小。这些时候，与静态资源不同的是，`你需要手动指定图片的尺寸`。同时我们强烈建议你使用 https 以满足 iOS [App Transport Security](https://segmentfault.com/a/1190000002933776) 的要求。

```javascript
// 正确
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}}
       style={{width: 400, height: 400}} />

// 错误
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}} />
```



### 网络图片的请求参数

可以在 Image 组件的 source 属性中指定一些请求参数，如下面的示例：

![Test](/assets/images/photo.png)

ps：因为某些不明原因，只能发图？！！∑(´△｀)？！


## 背景图片与嵌套写法

要实现Web 中的背景图（`background-image`），只需使用`<ImageBackground>`组件（其 props 与`<Image>`完全相同），然后把需要背景图的子组件嵌入其中即可。

```javascript
return (
  <ImageBackground source={...} style={{width: '100%', height: '100%'}}>
    <Text>Inside</Text>
  </ImageBackground>
);
```

注意：必须指定宽高样式。





## iOS 边框圆角的注意事项

请注意下列边框圆角样式目前在 iOS 的图片组件上还不支持：

- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomLeftRadius`
- `borderBottomRightRadius`



更多react-native图片相关的信息，移步[这里](https://reactnative.cn/docs/images/)



## 图片命名规则

在项目中所使用的图片命名规则

**图片业务（可选）+ 图片功能类别（必选） +  图片模块名称（可选）+ 图片精度 （可选）**

- 图片业务：
  - wx_ ：微信
  - qq_ ：QQ
  - jd_ : 东京商城
  - ...

- 图片功能类别：
  - mod-：是否公共，可选
  - icon：模块类固化的图标
  - logo：LOGO类
  - spr：单页面各种元素合并集合
  - btn：按钮
  - bg：可平铺或者大背景
  - …

- 图片模块名称：
  - goodslist：商品列表
  - goodsinfo：商品信息
  - userava tar：用户头像
  - …

- 图片精度：
  - 普清：@1x
  - Retina：@2x | @3x
  - …



如下面例子：

```
公共模块：
wx-mod-btn-goodlist@2x.png
wx-mod-btn-goodlist.png
mod-btn-goodlist.png

非公共模块：
wx-btn-goodlist@2x.png
wx-btn-goodlist.png
btn-goodlist.png
```



图片命名规范参考自[这里](https://jusfoun-fe.github.io/guide/docs/name/image.html)