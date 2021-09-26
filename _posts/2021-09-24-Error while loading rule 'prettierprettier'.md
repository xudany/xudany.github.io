---
title: Error while loading rule 'prettier/prettier'
date: 2021-09-24
categories:
- ESLint
tags:
- ESLint
- prettier
- ts
- issue
---

配置安装ESLint的时候出现了下面的报错：

Error while loading rule 'prettier/prettier'

详情报错

```bash
Error while loading rule 'prettier/prettier': context.getPhysicalFilename is not a function
```



网上找了各种方法：

1. 删除依赖 node_modules ，重装

   对于我的情况来说，无效

2. 检查是否忘记安装  `prettier` 插件
	```bash
	$ yarn add prettier
	```
	我已经安装了。

3. 修改 eslint 配置文件 .eslintrc.js 中的 `prettier` 插件
	
	```js
	{
	  "extends": [
	    "airbnb/base",
	    "prettier"
	  ],
	  "plugins": [
	    "prettier"
	  ]
	}
	```
	改为：
	
	```js
	{
	  "extends": [
	    "airbnb/base",
	    "eslint-config-prettier"
	  ],
	  "plugins": [
	    "eslint-plugin-prettier"
	  ]
	}
	```
	对于我的情况来说，无效
	
4. 升级 ESLint 的版本

   ```bash
   $ yarn upgrade -R eslint
   ```
   对于我的情况来说，无效

5. 在 eslint 配置文件 .eslintrc.js 中的 `rules` 增加 "prettier/prettier": 1

   ```js
   "rules": {
      "prettier/prettier": 1,
   },
   ```
   对于我的情况来说，无效
   
6. 把 `eslint-plugin-prettier` 恢复到 3.1.4 版本

   总算解决了！！！

 

参考资料

[Definition for rule 'prettier/prettier' was not found prettier/prettier ](https://github.com/prettier/eslint-plugin-prettier/issues/20)

[context.getPhysicalFilename is not a function](https://issueexplorer.com/issue/prettier/eslint-plugin-prettier/434)