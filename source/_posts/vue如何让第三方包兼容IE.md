---
title: vue如何让第三方包兼容IE
date: 2019-03-08 13:25:25
tags:
- node_modules
- 兼容IE
categories: vue
---

# vue如何让第三方包兼容IE

我们在使用vue的过程中会发现安装了第三方包，却无法在IE浏览器下正常运行的情况，这是因为vue-cli为了打包速度，默认情况下是不会使用babel-loader去处理node_modules下的第三方包的，这里有三种解决方案：

## 1. 将第三方包移出node_modules文件夹

将包从node_modules中拷贝出来，放入components文件夹下，再将import包的路径改为components下的路径，这样就可以在自己的项目中使用babel-loader去编译第三方的包了，但是这样做很不优雅。

## 2. 在vue.config.js中配置webpack使babel-loader去编译node_modules中的包

```javascript
module.exports = {
  configureWebpack: {
    module: {
      rules: [
        {
          test: /\.js$/,
          exclude: /\/node_modules\/tree-table-vue\//,
          use: {
            loader: "babel-loader"
          }
        }
      ]
    }
  }
};
```

这里有个疑问一直没搞明白，exclude 是排除的意思，webpack官方文档也是这样解释的，但是这个文件默认就是排除掉的，按照官方文档，应该使用include包含这个路径，但是使用include却无效，而使用exclude是有效的。

## 3. 终级解决方案，vue-cli的配置项transpileDependencies

```javascript
module.exports = {
  transpileDependencies: ["tree-table-vue"]
};
```