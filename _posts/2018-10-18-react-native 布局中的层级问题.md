---
title: react-native 布局中的层级问题
date: 2018-10-18
categories:
- react-native 
tags:
- react-native
- issue
---
## react-native 布局中的层级问题

默认情况下组件按其在文档树的顺序依次渲染，所以在代码结构上靠后的组件会覆盖前面的组件（如果它们在布局上有重叠的部分）。

`zIndex`控制着组件的堆叠覆盖顺序。它的表现和 CSS 上的`z-index`一致——`zIndex`大的在上面。



问题：使用zIndex来控制组件的层级，在Android中不生效，甚至有元素不显示的问题。



原因：默认情况下，使用`position: 'absolute'`后，在z轴的层叠关系由其摆放位置决定的，后面的元素会默认覆盖在前面的元素之上。

shadow（阴影）开头的样式现在可以在iOS上应用，但在Android中是不生效的，而Android上对应的属性是elevation。设置elevation属性就等价于使用原生的[elevation API](https://developer.android.com/training/material/shadows-clipping.html#Elevation)，因而也有同样的限制（比如最明显的就是需要Android 5.0以上版本）。此外还会影响到层叠视图在空间z轴上的顺序。



结论：

两个同一层级的定位组件（position：“absolute”）

- 对于Android

   1、 既没有ZIndex属性，又没有elevation 属性时，在z轴的层叠关系由其摆放位置决定的，放在下面的组件会在上层；
   2、 两个组件只有zIndex没有elevation属性时，zIndex大的在上层
   3、 两个组件有elevation属性时，elevation大的在上层
   4、 两个组件既有zIndex属性elevation属性时，以elevation为准

- 对于IOS，同层级的组件，z轴的层叠关系只与摆放顺序与zIndex有关，与elevation无关



解决方法： 

尽量改变组件的顺序，而不用zIndex或者判断是否是Android，使用elevation来替代zIndex



[参考链接](https://www.jianshu.com/p/d8ec3f367f31)

[参考链接](https://www.cnblogs.com/star91/p/ReactNative-cai-keng-zong-jie.html)