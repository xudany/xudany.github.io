---
title: 解决Cannot use import statement outside a module的报错问题
date: 2021-01-10
categories:
- next.js
tags:
- next.js
- ant-design-charts
- issue
---

随手一记录。

在 next.js 中引用ant-design-chart，使用的是官网快速开始的[实例代码](https://charts.ant.design/guide/start)

代码：

```tsx
import React, { Component } from 'react';
import { Line } from '@ant-design/charts';
class Page extends Component {
  render() {
    const data = [
      { year: '1991', value: 3 },
      { year: '1992', value: 4 },
      { year: '1993', value: 3.5 },
      { year: '1994', value: 5 },
      { year: '1995', value: 4.9 },
      { year: '1996', value: 6 },
      { year: '1997', value: 7 },
      { year: '1998', value: 9 },
      { year: '1999', value: 13 },
    ];
    const config = {
      data,
      height: 400,
      xField: 'year',
      yField: 'value',
      point: {
        size: 5,
        shape: 'diamond',
      },
    };
    return <Line {...config} />;
  }
}
export default Page;
```

开发模式下是可以正常使用的，但是打包的时候就报错了：

```bash
SyntaxError: Cannot use import statement outside a module
This error happened while generating the page. Any console logs will be displayed in the terminal window.
···
```



解决方法：

使用 next.js 动态加载

```tsx
import dynamic from 'next/dynamic';
const Line = dynamic(
  () => import("@ant-design/charts").then((mod) => mod.Line) as any,
  { ssr: false }
)
```





参考资料：

[[BUG] Cannot use import statement outside a module #215](https://github.com/ant-design/ant-design-charts/issues/215)

