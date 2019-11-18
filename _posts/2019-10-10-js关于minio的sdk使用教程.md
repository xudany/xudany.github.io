---
title: js关于minio的sdk使用教程
date: 2019-10-10
categories:
- minio
tags:
- minio
- js
- Learning
---



## npm下载

```bash
Copynpm install --save minio
```



## ts包下载

```bash
Copynpm install --save-dev @types/minio
```



## 使用

```bash
var Minio = require('minio')

var minioClient = new Minio.Client({
    endPoint: 'play.min.io',
    port: 9000,
    useSSL: true,
    accessKey: 'Q3AM3UQ867SPQQA43P2F',
    secretKey: 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG'
});
```



## 上传

```javascript
var Fs = require('fs')
var file = '/tmp/40mbfile'
var fileStream = Fs.createReadStream(file)
var fileStat = Fs.stat(file, function(err, stats) {
  if (err) {
    return console.log(err)
  }
  minioClient.putObject('mybucket', '40mbfile', fileStream, stats.size, function(err, etag) {
    return console.log(err, etag) // err should be null
  })
})
```



## 下载

```javascript
var size = 0
minioClient.getObject('mybucket', 'photo.jpg', function(err, dataStream) {
  if (err) {
    return console.log(err)
  }
  dataStream.on('data', function(chunk) {
    size += chunk.length
  })
  dataStream.on('end', function() {
    console.log('End. Total size = ' + size)
  })
  dataStream.on('error', function(err) {
    console.log(err)
  })
})
```



## 遇到的问题

putObject这个api只能接收stream/Buffer的类型，但是前端的文件是file的类型，刚开始百思不得其解，不知道要怎么使用，后来想着要不就找找能不能把file转成buffer，对我而言，已知base64是可以转成buffer，那file能不能转成base64呢，这样问题不就解决了，所以下面开始就是根据这个思路进行折腾。

```javascript
var Minio = require('minio');
//file=>base64
getBase64(img, callback) {
  const reader = new FileReader();
  reader.addEventListener('load', () => callback(reader.result));
  reader.readAsDataURL(img);
}

getBase64(file.file, imageUrl => {
  // 数据处理
  const base64 = imageUrl.split(',').pop();
  // base64转buffer
  const buffer = Buffer.from(base64, 'base64');
  var minioClient = new Minio.Client({
    endPoint: MINIO_CONFIG.endPoint,
    port: MINIO_CONFIG.port,
    useSSL: MINIO_CONFIG.useSSL,
    accessKey: localStorage.getItem('access_token'),
    secretKey: MINIO_CONFIG.secretKey,
  });

  let timestamp = `logo${new Date().getTime()}`;

  minioClient.putObject(MINIO_CONFIG.bucketName, timestamp, buffer, (err, etag) => {
    if (err) {
      message.error('上传图片失败，请稍后重试！');
    } else {
      console.log(etag);
      minioClient.getPartialObject(
        MINIO_CONFIG.bucketName,
        timestamp,
        0,
        (err, dataStream) => {
          if (err) {
            return console.log(err);
          }
          dataStream.on('error', function(err) {
            console.log(err);
          });    
          ......
        });
      });
    });
```

