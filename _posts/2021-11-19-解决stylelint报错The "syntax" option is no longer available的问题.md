---
title: 解决stylelint报错The "syntax" option is no longer available的问题
date: 2021-11-19
categories:
- next.js
tags:
- next.js
- react
- stylelint
- issue
---

记录一下这个疑难杂症。

详细报错信息：

```bash
The "syntax" option is no longer available. You should install an appropriate syntax, e.g. postcss-scss, and use the "customSyntax" option

```



解决方法：

版本问题，主要是`postcss-less`和`stylelint`的版本兼容问题

```json
{
    "devDependencies": {
    ...
    "less": "3.11.3",
    "less-loader": "6.1.0",
    "postcss-less": "3.1.4",
    "stylelint": "13.6.1",
    "stylelint-config-prettier": "8.0.2",
    "stylelint-config-rational-order": "0.1.2",
    "stylelint-config-standard": "20.0.0",
    "stylelint-declaration-block-no-ignored-properties": "2.3.0",
    "stylelint-order": "4.1.0"
    ...
  }
}
```





参考资料：

[stylelint for less - The "syntax" option is no longer available](https://github.com/stylelint/stylelint/issues/5663)

[TypeError: this.getPosition is not a function in LessParser.inlineComment](https://github.com/stylelint/stylelint/issues/5666)