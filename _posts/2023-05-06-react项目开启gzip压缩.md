---
title: react项目开启gzip压缩
date: 2023-05-06
categories:
- react
tags:
- gzip
- react
- CompressionPlugin
- react-app-rewired
- customize-cra
---



## react项目开启gzip压缩

### 背景

最近在做网页的性能优化，其中一项优化就是使用 Gzip 压缩，它可以在发送到浏览器之前将静态资源进行压缩，然后由浏览器解压缩。


### 实现

react项目可以添加并设置`CompressionPlugin`来进行gzip压缩。

#### 安装

```bsah
npm install compression-webpack-plugin --save-dev
// 或者
yarn add compression-webpack-plugin --dev
```

#### 配置文件

config-overrides.js

```js
const { override, addWebpackPlugin } = require("customize-cra");
const CompressionPlugin = require("compression-webpack-plugin");

/**
 * 开启gzip压缩
 */
const Compression = new CompressionPlugin({
    filename: "[path].gz[query]",
    algorithm: "gzip",
    test: /\.js$|\.css$|\.html$/,
    threshold: 10240,
    minRatio: 0.8
});

module.exports = override(
  // 其他配置
  ...
  // 添加压缩插件
	addWebpackPlugin(Compression),
);
```

这个配置中，`CompressionPlugin`的选项表示：

*   `filename`：生成的文件名。这里，它将原始文件路径保留，并在其后添加`.gz`扩展名。
*   `algorithm`：要使用的压缩算法。这里使用gzip。
*   `test`：确定哪些文件应被压缩的正则表达式。在这个例子中，压缩所有JS、CSS和HTML文件。
*   `threshold`：只处理比这个值大的资源。这里设置为10KB。
*   `minRatio`：只有压缩率小于这个值的资源才会被处理。

#### 服务器nginx配置

上面设置后，在构建项目时，会生成对应的gzip文件，但服务器还需要配置相应的gzip静态文件路由。

```nginx
server {
    listen       80;
    server_name  localhost;
    
    # 其他配置...

    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    location ~* \.(js|css)$ {
        gzip_static on; # 优先服务预先压缩的文件
        expires max;
        add_header Cache-Control public;
    }
   
  	# 其他配置...
}
```

这些 Gzip 配置项的含义如下：

*   `gzip on;` - 启用 gzip 压缩
*   `gzip_vary on;` - 向响应头添加 Vary: Accept-Encoding，用于告知代理服务器是否缓存
*   `gzip_min_length 10240;` - 文件大小超过 10240 bytes 才进行压缩，可以防止对小文件进行不必要的压缩
*   `gzip_proxied expired no-cache no-store private auth;` - 控制在哪些代理服务器缓存的页面可以压缩
*   `gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml;` - 设置哪些 MIME 类型的文件可以压缩
*   `gzip_disable "MSIE [1-6]\.";` - 对于老旧的不支持 Gzip 压缩的 IE 浏览器不进行压缩

如果之前已有一些静态资源的路径配置，那需要在原来基础上进行补充，比如：

```nginx
    location ~* /static/.*\.(.*)$ {
        rewrite ".*/static/(.*)" "/static/$1" break;
        root /usr/share/nginx/html/;
        gzip_static on;
        expires max;
        add_header Cache-Control public;
    }
```



### 验证

打开浏览器的开发者工具，然后转到"Network"选项卡，然后在"Network"选项卡中选择一个已加载的资源，如一个JS或CSS文件。在HTTP响应头（Response Headers）中，应该可以看到"Content-Encoding: gzip"这一行文字，这表明该资源已被Gzip压缩。


### 参考资料
[How to Enable GZIP Compression for Faster Web Pages](https://blog.hubspot.com/website/gzip-compression)
[你真的了解 gzip 吗？](https://zhuanlan.zhihu.com/p/24764131)
