---
title: 搭建 ts + scss + json-server 的微信小程序工程
date: 2021-07-17
categories:
- WeChat
tags:
- WeChat
- ts
- scss
- mock
- json-srver
---


嗯，总之就是小程序了（撸起袖子）


### 目录结构

打开微信开发者工具，新建项目的时候，语言选择TypeScript，然后就会自动生成下面的TS项目的模板。

```
|-- miniprogram           
    |-- pages
    |-- utils
    |-- app.js
    |-- app.json
    |-- app.ts
    |-- app.wxss
    |-- sitemap.json
|-- node_modules
|-- typings
|-- package.json
|-- project.config.json
|-- tsconfig.json
```

然后自己加了一些，最终的目录结构是

```
|-- miniprogram
		|-- api
		|-- components
		|-- images
		|-- miniprogram_npm
    |-- pages
    |-- router
    |-- style
    |-- utils
    |-- app.js
    |-- app.json
    |-- app.ts
    |-- app.wxss
    |-- sitemap.json
|-- mock
|-- node_modules
|-- typings
|-- package.json
|-- project.config.json
|-- tsconfig.json
```
- miniprogram
  - api：存放api文件的
  - component： 自定义的微信小程序组件
  - iamges： 图片
  - miniprogram_npm：依赖
  - pages：微信小程序页面组成
  - router：路由相关
  - style： 公共样式相关
  - utils：工具类
  - app.`*`：入口文件
- mock： mock数据
- project.config.json： 微信小程序工程配置文件
- tsconfig.json：TS配置文件



### api的封装和处理

#### 目录结构

```
|-- api
		|-- api.json
		|-- api.yaml
		|-- createApi.js
		|-- http.ts
		|-- request.ts
```
- api.json、api.yaml： 接口文档
- createApi.js：根据接口文档自动生成接口模块
- http.ts：接口地址的配置文件
- request.ts：封装请求接口



#### 封装请求接口

request.ts
```ts
interface RequestSuccessCallbackResult extends WechatMiniprogram.RequestSuccessCallbackResult {
    data: any;
}

type MethodsProps = 'OPTIONS' | 'GET' | 'HEAD' | 'POST' | 'PUT' | 'DELETE' | 'TRACE' | 'CONNECT'

const header = {
    'content-type': 'application/json; charset=utf-8',
}

/**
 * @description: 
 * @param {any} params
 * @return {*}
 */
const dataPramas = (params: any) => {
    return Object.assign({}, params, {

    })
}

/**
 * @description: 分装请求方法
 * @param {*} url 地址
 * @param {*} params 请求参数
 * @param {*} method 请求方法
 * @param {*} resolve 成功回调
 * @param {*} reject 失败回调
 */
const request = (url: string, params: any, method: MethodsProps, resolve: any, reject: any) => {
    wx.showLoading({
        title: '数据加载中...',
    });
    wx.request({
        header: header,
        url: url,
        method: method,
        data: dataPramas(params),
        success: (res: RequestSuccessCallbackResult) => {
            wx.hideLoading();
            if (res.data) {
                // 判断请求成功的状态码
                if (res.data.code === 0) {
                    resolve(res.data.data);
                } else {
                    reject(res.data.data);
                }
            }
        },
        fail: (error) => {
            wx.hideLoading();
            reject(error)
        }
    })
}

/**
 * @description: get请求方式
 * @param {string} url
 * @param {*} params
 * @return {*}
 */
const getGequest = (url: string, params = {}) => {
    return new Promise((resolve, reject) => {
        request(url, params, "GET", resolve, reject);
    }) as Promise<any>
}

/**
 * @description: post请求方法
 * @param {string} url
 * @param {*} params
 * @return {*}
 */
const postRequest = (url: string, params = {}) => {
    return new Promise((resolve, reject) => {
        request(url, params, "POST", resolve, reject);
    }) as Promise<any>
}


export default { request, get: getGequest, post: postRequest }
```



#### 自动生成接口模块

createApi.js

```js
const fs = require("fs");
const path = require("path");

// 首字母大写
const toUpper = (item) => {
  return item.slice(0, 1).toUpperCase() + item.slice(1).toLowerCase();
};

// 读取JSON配置文件
const readConfigFile = () => {
  return JSON.parse(
    fs.readFileSync(path.dirname(__filename) + "/api.json", "utf8")
  );
};

const parserPaths = (paths) => {
  const arr = Object.entries(paths);
  paths = arr.map(([path, value]) => {
    const methods = Object.entries(value);
    const names = path.split("/");
    const name = names
      .map((item) => {
        return toUpper(item);
      })
      .join("");
    for (const [method, params] of methods) {
      let [, url] = path.split(params.tags[0]);
      url = "`${urls." + params.tags[0] + "}" + (url === "/" ? "" : url) + "`";
      return {
        method,
        fcName: method + name,
        url,
        tag: params.tags[0],
        text: `
    // ${params.summary}
    ${method + name}: (params: any) => {
        return new Promise((resolve) => {
            resolve(request.${method}(${url}, params))
        }) as Promise<any>
    },`,
      };
    }
  });

  return paths;
};

const parserContent = (tags) => {
  const tagContent = tags.map((item) => {
    const content = `
import request from "../request";
import urls from "../http";

const ${toUpper(item.name)}Api = {
    ${item.apiText.join("\n")}
}

export default ${toUpper(item.name)}Api;`;
    return {
      ...item,
      content,
    };
  });

  return tagContent;
};

const createDir = (tags) => {
  tags.forEach((item) => {
    fs.mkdirSync(path.dirname(__filename) + `/${item.name}`, {
      recursive: true,
    });
  });
};

const writeFile = (tagsModel) => {
  tagsModel.forEach((item) => {
    fs.writeFileSync(
      path.dirname(__filename) + `/${item.name}/${item.name}.ts`,
      item.content,
      "utf8"
    );
  });
};

const createApi = () => {
  const config = readConfigFile();
  createDir(config.tags);
  const parserPath = parserPaths(config.paths);
  const tagsModel = config.tags.map((item) => {
    return {
      ...item,
      apiText: [],
    };
  });
  for (const item of parserPath) {
    for (const index in tagsModel) {
      if (item.tag === tagsModel[index].name) {
        tagsModel[index].apiText.push(item.text);
      }
    }
  }
  const fileContent = parserContent(tagsModel);
  writeFile(fileContent);
};

createApi();

```



### 使用 json-server 生成 mock 数据

[json-server](https://github.com/typicode/json-server) 是一个 Node 模块，可在前端本地运行，可存储 json 数据，能快速搭建一个后端服务来进行数据模拟。

#### 安装

```bash
$ npm install json-server
```



#### 目录结构

```
|-- mock
		|-- db.json
		|-- json-server.json
		|-- route.json
```

- db.json： 数据源
- json-server.json：服务配置文件
- route.json：路由配置文件

db.json

```json
{
    "login": {
        "code": 0,
        "msg": "success",
        "data": {
            "openid": "sahdfjkhyewoukryqiwelrhj",
            "sessionKey": "sfsdfkjashfuekrjhrfjkwhe"
        }
    },
    "banner": {
        "code": 0,
        "msg": "success",
        "data": []
    },
    "goosList": {
        "code": 0,
        "msg": "success",
        "data": []
    },
    "data3": [
        {
            "id": "004",
            "name": "Rebeca",
            "age": 27
        }
    ]
}
```

json-server.json

```josn
{
    "port": 3001,
    "watch": true,
    "static": "./public",
    "read-only": false,
    "no-cors": false,
    "no-gzip": false,
    "routes": "route.json"
}
```


route.json
```json
{
  "/api/*": "/$1",   //   /api/test   ==  /test
  "/:resource/:id/show": "/:resource/:id",
  "/posts/:category": "/posts?category=:category",
  "/articles\\?id=:id": "/posts/:id"
}
```



#### 命令配置

在 package.json 中增加 mock 命令

```diff
"scripts": {
+   "mock": "cd mock && json-server --watch -c json-server.json db.json"
}
```

运行 `npm run mock` 输出类似下面的内容，说明启动成功

```bash
  \{^_^}/ hi!

  Loading db.json
  Loading route.json
  Done

  Resources
  http://localhost:3001/login
  http://localhost:3001/banner
  http://localhost:3001/goosList
  http://localhost:3001/data3

  Home
  http://localhost:3001
```

访问 http://localhost:3001 就能获取到json的数据






### 微信小程序开发时注意的坑

#### 1. 微信小程序使用ts模板不会自动编译成js

问题：微信小程序使用ts模板不会自动编译成js

解决：在微信开发者工具右上角点击详情，选择本地设置，把 `将JS编译成ES5`、`使用 npm 模块`、`启用自定义处理命令`三个选项勾上就可以了。


#### 2. 微信小程序 Error: 执行自定义预览前预处理命令失败!

问题：预览的时候报错：Error: 执行自定义预览前预处理命令失败!

解决：如果使用的是TS模板的情况下，在微信开发者工具右上角点击详情，选择本地设置，把 `启用自定义处理命令`这个选项取消勾选就可以了。


#### 3. 微信小程序的 absolute 样式表现异常

问题：在微信小程序中css布局使用 `absolute`，设置了`top`，不设置`left`或者`right`,在开发环境中，元素会自动居中，但是在真机调试的时候，`left`会变成0px的表现样式。

解决：同时设置`top`和`left`属性。


#### 4. 微信小程序的 padding 样式表现异常

问题：在微信小程序中设置`padding`样式，开发环境表现正常，但是真机调试的时候，样式布局都改变了。_(:з」∠)_ 

解决：设置了`padding`样式的模块都加上`box-sizing: border-box;`。



### 参考资料

[json-server全攻略](https://www.jianshu.com/p/9cfc5cdb0aeb)

[json-server 详解](https://www.cnblogs.com/fly_dragon/p/9150732.html)



-- 未完 --