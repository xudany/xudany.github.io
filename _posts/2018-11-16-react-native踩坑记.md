---
title: react-native入门学习笔记、react-native踩坑记、越踩坑越多的react-native、react-native从入门到放弃、鬼知道我经历了什么的react-native
date: 2018-11-16
categories:
- react-native 
tags:
- react-native
- Learning 
---

## react-native入门学习笔记、react-native踩坑记、越踩坑越多的react-native、react-native从入门到放弃、鬼知道我经历了什么的react-native

为什么标题那么长？因为适合。（微笑）~~长吗？再长的标题都不能表达我内心强烈的吐槽欲望~~~~(╯°Д°)╯︵┻━┻~~

终于开始react-native了，记得有一位伟人曾经说过，“要学习一门新技术，要从官方文档开始。”

所以，走你！ [React Native 中文网 ](https://reactnative.cn/docs/getting-started/)

ps：本文开发环境为macOS



## 搭建开发环境

万事开头难，开发环境永远是最难的，经常出现“明明是按照步骤做的，为什么还会报错？！”以及“我刚刚好像漏做了什么，怎么就跑成功了？！”这种怀疑人生、怀疑自己问题。总之，务必按照官方文档一步一步地走呀！记录下我遇到的问题。

### iOS开发环境

开发平台：macOS

目标平台：iOS

必须安装的依赖：Node、Watchman 和 React Native 命令行工具 

编译工具：Xcode

报错：创建新项目，`react-native init AwesomeProject`命令长时间无响应，或报错`shasum check failed`

报错原因：没有科学上网

解决方法：请科学上网或者仓库源替换为国内镜像

```
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```

PS： 推荐科学上网的解决方法，不然后续会有一系列麻烦事 _(:з」∠)_

报错：

```
no bound .....

XCode报错 "_BZ2_bzRead", referenced from ......
```

报错原因：不详   _(:з」∠)_

解决方法：在工程Xcode中选中工程，然后在工程target的Build Phases->Link Binary with Libraries中加入libz.tbd、libbz2.1.0.tbd



### Android开发环境

开发平台：macOS

目标平台：Android

必须安装的依赖：Node、Watchman 和 React Native 命令行工具以及 JDK

编译工具：Android Studio

提示：安装Android的开发环境有点烦，最好能科学上网，如果报错中出现有网址，那么 99% 就是无法正常翻墙，以及特别注意安装的JDK的版本是1.8。

报错：运行`react-native run-android`时报错，报错信息中含有`Could not find tools.jar`等字样

报错原因：JDK的问题

解决方法：重装JDK1.8，注意目前不能使用更高版本



### 创建新项目

使用 React Native 命令行工具来创建一个名为"AwesomeProject"的新项目：

官方强调！init 命令默认会创建最新的版本，而目前最新的 0.45 及以上版本需要下载 boost 等几个第三方库编译。这些库在国内即便翻墙也很难下载成功，导致很多人`无法运行iOS项目`！！！中文网在论坛中提供了这些库的[国内下载链接](http://bbs.reactnative.cn/topic/4301/)。如果嫌麻烦，又没有对新版本的需求，那么可以暂时创建`0.44.3`的版本。

```
react-native init AwesomeProject
```

提示：可以使用`--version`参数（注意是`两`个杠）创建指定版本的项目。例如`react-native init MyApp --version 0.44.3`。注意版本号必须精确到两个小数点。



## 实例教程：Hello Word

以下是在官方文档例子上加一点修改，一些基本的概念，比如 JSX 语法、组件、`state`状态以及`props`属性、样式等详细讲解都可以查看官方文档，下面简单归纳一下。

在生成的项目文件App.js

```javascript
import React, { Component } from 'react';
import { Text, View, StyleSheet } from 'react-native';

class HelloWorldApp extends Component {
  constructor(props) {
    super(props);
    this.state = { showText: true };

    // 每1000毫秒对showText状态做一次取反操作
    setInterval(() => {
      this.setState(previousState => {
        return { showText: !previousState.showText };
      });
    }, 1000);
  }
  render() {
    // 根据当前showText的值决定是否显示text内容
    let display = this.state.showText ? this.props.text : ' ';
    return (
        <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}}>
          <Text style={[styles.bigblue, {color: 'red'}]}>{display}</Text>
        </View>
    );
  }
}

export default class BlinkApp extends Component {
  render() {
    return (
      <View>
        <HelloWorldApp text='Hello world' />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  bigblue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30,
  }
});
```



### Props（属性）

大多数组件在创建时就可以使用各种参数来进行定制。用于定制的这些参数就称为`props`（属性）。

例子中的`<Text style={[styles.bigblue, {color: 'red'}]}>{display}</Text>`就是使用名为`style`的prop来控制字体的颜色。

自定义的组件也可以使用`props`。只需在`render`函数中引用`this.props`，然后按需处理即可。

例子中的`<HelloWorldApp text='Hello world' />` 父组件定义`text`属性的值。

在子组件里面引用`let display = this.state.showText ? this.props.text : ' ';`



### State（状态）

一般使用两种数据来控制一个组件：`props`和`state`。`props`是在父组件中指定，而且一经指定，在被指定的组件的生命周期中则不再改变。 对于需要改变的数据，需要使用`state`。

首先，需要在 constructor 中初始化`state`，如上述例子中的这一部分

```javascript
  constructor(props) {
    super(props);
    this.state = { showText: true };
  }
```

然后在需要修改时调用`setState`方法

```javascript
  this.setState(previousState => {
    return { showText: !previousState.showText };
  });
```

**<u>关于`setState`需要特别注意的事情！！！</u>**

- 每次调用`setState`时， render 方法都会重新渲染，所以一切界面的变化都是状态`state`的变化
- `state`的修改必须通过`setState()`方法，像`this.state.showText = true`这样的直接赋值修改是无效的
- setState 是一个 merge 合并操作，只修改指定属性，不影响其他属性。
- setState是异步操作，修改不会马上生效。这个在开发中很容易成为坑！！我摔过！！(╯°□°）╯︵┻━┻   参考这里 [react-native 中 setState 的坑]()



### 样式

所有的核心组件都接受名为`style`的属性，使用这个属性来改变样式，样式名基本上是遵循了 web 上的 CSS 的命名，只是按照 JS 的语法要求使用了驼峰命名法，例如将`background-color`改为`backgroundColor`。

`style`属性可以传入一个数组——在数组中位置居后的样式对象比居前的优先级更高，这样可以间接实现样式的继承。如`<Text style={[styles.bigblue, {color: 'red'}]}>{display}</Text>`，后面的红色样式覆盖了前面的蓝色样式。

使用`StyleSheet.create`来集中定义组件的样式，如上面例子

```javascript
const styles = StyleSheet.create({
  bigblue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30,
  }
});
```



### 高度和宽度

React Native 中的尺寸都是无单位的，表示的是与设备像素密度无关的逻辑像素点。

最简单的给组件设定尺寸的方式就是在样式中指定固定的`width`和`height`。如上述例子中的`<View style={{width: 50, height: 50, backgroundColor: 'powderblue'}}>`

也可以设置弹性宽高（Flex）使其在可利用的空间中动态扩张或收缩。

使用`flex:1`来指定某个组件扩张以撑满所有剩余的空间。多个并列的子组件使用了`flex:1`，则这些子组件会平分父容器中剩余的空间。

并列的子组件的`flex`值不一样，则谁的值更大，谁占据剩余空间的比例就更大（即占据剩余空间的比等于并列组件间`flex`值的比）。

注意：组件能够撑满剩余空间的前提是其父容器的尺寸不为零。如果父容器既没有固定的`width`和`height`，也没有设定`flex`，则父容器的尺寸为零。其子组件如果使用了`flex`，也是无法显示。



### 布局

在 React Native 中使用 flexbox 规则来指定某个组件的子元素的布局。Flexbox 可以在不同屏幕尺寸上提供一致的布局结构。

React Native 中的 Flexbox 的工作原理和 web 上的 CSS 基本一致，当然也存在少许差异。首先是默认值不同：`flexDirection`的默认值是`column`而不是`row`，而`flex`也只能指定一个数字值。

一般来说，使用`flexDirection`、`alignItems`和 `justifyContent`三个样式属性就已经能满足大多数布局需求。

#### FlexDirection

在组件的`style`中指定`flexDirection`可以决定布局的**主轴**。默认值是**竖直轴(column)**方向。

#### JustifyContent

在组件的 style 中指定`justifyContent`可以决定其子元素沿着**主轴**的**排列方式**。对应的这些可选项有：`flex-start`、`center`、`flex-end`、`space-around`、`space-between`以及`space-evenly`。

#### AlignItems

在组件的 style 中指定`alignItems`可以决定其子元素沿着**次轴**（与主轴垂直的轴，比如若主轴方向为`row`，则次轴方向为`column`）的**排列方式**。对应的这些可选项有：`flex-start`、`center`、`flex-end`以及`stretch`。

注意：要使`stretch`选项生效的话，子元素在次轴方向上不能有固定的尺寸。

对于布局有影响的完整样式列表记录在[这篇文档中](https://reactnative.cn/docs/layout-props)。



### 其他基础组件

其他一些基础组件，比如处理文本输入的`TextInput`、处理触摸事件的 Touchable 系列组件、`Button`、处理滚动视图的`ScrollView`、用于长列表的`FlatList`和`SectionList`等，文档均有详细讲解。

可以在文档网站的左侧看到组件的完整列表。可以先看一下下面这个简单的分类：

- [基础组件](https://reactnative.cn/docs/components-and-apis#%E5%9F%BA%E7%A1%80%E7%BB%84%E4%BB%B6)
- [交互控件](https://reactnative.cn/docs/components-and-apis#%E4%BA%A4%E4%BA%92%E6%8E%A7%E4%BB%B6)
- [列表视图](https://reactnative.cn/docs/components-and-apis#%E5%88%97%E8%A1%A8%E8%A7%86%E5%9B%BE)
- [iOS 独有组件](https://reactnative.cn/docs/components-and-apis#iOS%E7%8B%AC%E6%9C%89%E7%9A%84%E7%BB%84%E4%BB%B6%E5%92%8CAPI)
- [Android 独有组件](https://reactnative.cn/docs/components-and-apis#Android%E7%8B%AC%E6%9C%89%E7%9A%84%E7%BB%84%E4%BB%B6%E5%92%8CAPI)
- [其他](https://reactnative.cn/docs/components-and-apis#%E5%85%B6%E4%BB%96)



其中的一些组件有坑，可参考

[react-native 中 FlastList 的坑]()

[react-native 中 SectionList 的坑]()

都是一步一坑这样踩着过来的  T^T



## 调试

好了，环境搭建完了，组件会写，页面也会布局了，接下来就是如何调试的问题，以下是我常用的调试方法和工具，更多相关信息移步 [调试· react-native](https://reactnative.cn/docs/debugging/#docsNav)

**<u>注：这部分是在开发平台为macOS下的调试！！！</u>**

### 开启调试的快捷键

React Native 在 iOS 模拟器上支持一些快捷键操作，要使用快捷键请**务必确保模拟器的 Hardware 菜单中，Keyboard 选项下的"Connect Hardware Keyboard"处于开启状态**，否则按键是没有响应的。



### 访问 App 内的开发菜单

#### 真机

- 可通过摇晃真机设备打开开发菜单

#### iOS 模拟器

- "Hardware"菜单中的"Shake Gesture"选项打开

- **Command**`⌘` + **D** 快捷键 打开

#### Android 模拟器

- 按下 **Command**`⌘` + **M**

- 直接在命令行中运行`adb shell input keyevent 82`来发送菜单键命令。



### 刷新 JavaScript

#### 手动刷新

具体的操作就是在上面打开的开发菜单中点击"Reload"选项。

iOS 模拟器按下**Command**`⌘` + **R** 

Android 模拟器上对应的则是按两下**R** ，即为 **R** + **R** 

#### 自动刷新

选择开发菜单中的"Enable Live Reload"可以开启自动刷新

如果要实现热重载，只需开启开发菜单中的[Hot Reloading](https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html)选项。

如果某些情况下热重载不能顺利实施，那就手动刷新。

必须要重新编译应用才能使修改生效：

- 增加了新的资源(比如给 iOS 的`Images.xcassets`或是 Andorid 的`res/drawable`文件夹添加了图片)
- 更改了任何的原生代码（objective-c/swift/java）



### 应用内的错误与警告提示（红屏和黄屏）

红屏或黄屏提示都只会在开发版本中显示，正式的离线包中是不会显示的。

#### 红屏错误

应用内的报错会以全屏红色显示在应用中（调试模式下），称为红屏（red box）报错。可以用用`console.error()`来手动触发红屏错误。

#### 黄屏警告

应用内的警告会以全屏黄色显示在应用中（调试模式下），称为黄屏（yellow box）报错。点击警告可以查看详情或是忽略掉。可以使用`console.warn()`来手动触发黄屏警告。在默认情况下，开发模式中启用了黄屏警告。



### Chrome 开发者工具

在开发者菜单中选择"Debug JS Remotely"选项，即可以开始在 Chrome 中调试 JavaScript 代码。

点击这个选项的同时会自动打开调试页面 <http://localhost:8081/debugger-ui>.(如果地址栏打开的是 ip 地址，则请自行改为 localhost)

在 Chrome 的菜单中选择`Tools → Developer Tools`可以打开开发者工具，也可以通过键盘快捷键来打开**Command**`⌘` + **Option**`⌥` + **I**。

打开[有异常时暂停（Pause On Caught Exceptions）](http://stackoverflow.com/questions/2233339/javascript-is-there-a-way-to-get-chrome-to-break-on-all-errors/17324511#17324511)选项，能够获得更好的开发体验。



### React Developer Tools

可以使用React Developer Tools来调试React组件层次结构。全局安装：

```
npm install -g react-devtools
```

安装完成后在命令行中执行`react-devtools`即可启动此工具：

```
react-devtools
```

还可以打开应用程序内开发者菜单，选择“Toggle Inspector”。它会弹出一个覆盖层，此时点击任何UI元素，并看到有关它的信息。只要再次在开发者菜单中点击“Toggle Inspector”即可退出次模式。

当react-devTools运行时，Inspector会进入特殊的折叠模式，而不是使用DevTools作为主要UI。在这种模式下，在模拟器中点击一些东西会在DevTools中显示相关组件。



### 性能监测

可以在开发者菜单中选择"Pref Monitor"选项以开启一个悬浮层，其中会显示应用的当前帧数。



### Android设备调试

对于 Android 5.0+设备（包括模拟器）来说，将设备通过 USB 连接到电脑上后，使用以下命令来验证设备是否连接。

```
adb devices
```

如果已连接，可以看到设备名称以“设备”形式列示。使用`adb`命令行工具来设定从设备到电脑的端口转发：

```
adb reverse tcp:8081 tcp:8081
```

注意：一些安卓机要现在本机上开启开发者模式才可调试

更多Android 调试桥的命令的信息可查看[这里](https://developer.android.com/studio/command-line/adb)



### 调试原生代码

在和原生代码打交道时（比如编写原生模块），可以直接从 Android Studio 或是 Xcode 中启动应用，并利用这些 IDE 的内置功能来调试（比如设置断点）。这一方面和开发原生应用并无二致。



## 第三方组件

记得有一位伟人曾经说过，“不要重复造轮子，要站在伟人的肩膀上开发。“ 

所以好用的第三方组件，走起，附上别人整理的一份[常用第三方组件汇总清单](https://shenbao.github.io/ishehui/html/RN%20%E5%9F%BA%E7%A1%80/React%20Native%20%E5%B8%B8%E7%94%A8%E7%AC%AC%E4%B8%89%E6%96%B9%E7%BB%84%E4%BB%B6%E6%B1%87%E6%80%BB.html)

基本上项目必备的：react-navigation、react-redux全家桶就不说了，可以移步

[react-navigation快速入门]()  [react-redux快速入门]()

然后记录一下我用过的RN第三方组件：

- 照片选择编辑：[react-native-image-crop-picker ](https://github.com/react-community/react-native-image-picker) 

  此插件很强大但使用时坑比较多，曾经不知流过多少辛酸泪，可参考  [react-native-image-crop-picker 避坑指南](https://xudany.github.io/react-native/2018/10/22/react-native-image-crop-picker-%E9%81%BF%E5%9D%91%E6%8C%87%E5%8D%97/)

- 图片轮播，放大，手势捏合：[react-native-image-zoom-viewer](https://github.com/magicwing/react-native-image-zoom-viewer)

- Tosat提示：[react-native-root-siblings](https://github.com/magicismight/react-native-root-siblings)  [react-native-root-toast](https://github.com/magicismight/react-native-root-toast)

- 侧滑按钮: [react-native-swipe-list-view](https://github.com/jemise111/react-native-swipe-list-view)

- 轮播视图: [react-native-swiper](https://github.com/leecade/react-native-swiper)

- 阴影：[react-native-shadow](https://github.com/879479119/react-native-shadow)

- 支持SVG：[react-native-svg](https://github.com/react-native-community/react-native-svg)


## 打包APK

Android 要求所有应用都有一个数字签名才会被允许安装在用户手机上，所以需要生成一个签名的 APK 包。



#### 1. 生成一个签名密钥

用`keytool`命令生成一个私有密钥

```
$ keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

这条命令会要求输入密钥库（keystore）和对应密钥的密码，然后设置一些发行相关的信息。最后会生成一个叫做`my-release-key.keystore`的密钥库文件。

在运行上面这条语句之后，密钥库里应该已经生成了一个单独的密钥，有效期为 10000 天。--alias 参数后面的别名是将来为应用签名时所需要用到的，所以记得记录这个别名。



#### 2. 设置 gradle 变量

1. 把`my-release-key.keystore`文件放到工程中的`android/app`文件夹下。
2. 编辑`项目目录/android/gradle.properties`。如果没有`gradle.properties`文件就创建一个，添加如下的代码（注意把其中的`****`替换为相应密码）

```
MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
MYAPP_RELEASE_KEY_ALIAS=my-key-alias
MYAPP_RELEASE_STORE_PASSWORD=*****
MYAPP_RELEASE_KEY_PASSWORD=*****
```



#### 3. 把签名配置加入到项目的 gradle 

编辑项目目录下的`android/app/build.gradle`，添加如下的签名配置：

```
...
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
                storeFile file(MYAPP_RELEASE_STORE_FILE)
                storePassword MYAPP_RELEASE_STORE_PASSWORD
                keyAlias MYAPP_RELEASE_KEY_ALIAS
                keyPassword MYAPP_RELEASE_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...

```



#### 4. 生成发行 APK 包

表示进入项目的文件android的目录下运行以下命令

```
$ ./gradlew assembleRelease
```

生成的 APK 文件位于`android/app/build/outputs/apk/app-release.apk`，它已经可以用来发布了。



#### 5. 测试应用的发行版本

在把发行版本提交到之前，可以做一次最终测试。输入以下命令可以在设备上安装发行版本：

```
$ react-native run-android --variant=release
```

注意：在 debug 和 release 版本间来回切换安装时可能会报错签名不匹配，此时需要先卸载前一个版本再尝试安装。





## 各种各样的坑

1. [react-native Xcode报错'config.h' file not found](https://xudany.github.io/react-native/2018/10/12/react-native-Xcode%E6%8A%A5%E9%94%99-'config.h'-file-not-found/)

2. [ReactNative中iOS和Android的兼容性问题]()

3. [Xcode报错 Build input file cannot be found /Users/.../node_modules/react-native/third-party/double-conversion-1.1.5/src](https://xudany.github.io/react-native/2018/10/12/Xcode%E6%8A%A5%E9%94%99-Build-input-file-cannot-be-found-Users...-node_modules-react-native-third-party-double-conversion-1.1.5-src/)

4. [react-native 图片使用问题](https://xudany.github.io/react-native/2018/10/13/react-native-%E5%9B%BE%E7%89%87%E4%BD%BF%E7%94%A8%E9%97%AE%E9%A2%98/)

5. [react-native-image-crop-picker 避坑指南](https://xudany.github.io/react-native/2018/10/22/react-native-image-crop-picker-%E9%81%BF%E5%9D%91%E6%8C%87%E5%8D%97/)

6. [react-native 常用组件之TextInput](https://xudany.github.io/react-native/2018/10/20/react-native-%E5%B8%B8%E7%94%A8%E7%BB%84%E4%BB%B6%E4%B9%8BTextInput/)

7. [react-native 布局中的层级问题](https://xudany.github.io/react-native/2018/10/18/react-native-%E5%B8%83%E5%B1%80%E4%B8%AD%E7%9A%84%E5%B1%82%E7%BA%A7%E9%97%AE%E9%A2%98/)

8. [react-native 项目结构](https://xudany.github.io/react-native/2018/11/02/react-native%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84/)

9. [eact-native 通讯录字母跳转分组功能]()



## 官方推荐的学习资源

[精心整理的资源列表](https://github.com/jondot/awesome-react-native) 

[React-native学习指南](https://github.com/reactnativecn/react-native-guide)



----踩坑不止，未完，将持续踩坑----