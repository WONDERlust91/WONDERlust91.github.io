---
title: node配置服务器跨域使pdfjs加载非同源文件
date: 2019-03-24 22:43:57
tags:
- express
- node
- pdfjs
- 跨域
categories: node
---

# node配置服务器跨域使pdfjs加载非同源文件

pdfjs因为同源策略，只能加载本地的pdf，想要加载远程的pdf就会报跨域的错，查看[官方文档](https://github.com/mozilla/pdf.js/issues/4530)后发现pdfjs本身并不阻止加载远程pdf文件，只要文件服务器配置好跨域即可。

这里使用了node中的express框架搭建了一个简单的静态文件服务器。

```javascript
// pdfjs官网issue提供的跨域配置方案
// {
//   "Access-Control-Allow-Origin": "*",
//   "Access-Control-Allow-Headers": "range",
//   "Access-Control-Expose-Headers": "content-range, content-length, accept-ranges",
//   "Access-Control-Allow-Methods": "*"
// }
// 引入express
const express = require("express");
const app = express();

// 根据官方文档中的issue配置跨域
app.all("*", function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "range");
  res.header(
    "Access-Control-Expose-Headers",
    "content-range, content-length, accept-ranges"
  );
  res.header("Access-Control-Allow-Methods", "*");
  next();
});

// 配置静态文件目录
app.use(express.static("public"));

// 监听服务器的某个端口
const server = app.listen(8082, function() {
  console.log("server-start");
});
```

配置好跨域后，你会发现还是报跨域的错，在pdfjs源码中查找file origin does not match viewer，就会发现源码中只要不是同源就会抛出异常，注释掉原码中的throw error即可。

```javascript
if (origin !== viewerOrigin && protocol !== 'blob:') {
  // throw new Error('file origin does not match viewer\'s');
}
```