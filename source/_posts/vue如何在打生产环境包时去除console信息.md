---
title: vue如何在打生产环境包时去除console信息
date: 2019-03-24 20:54:21
tags:
- vue
- 去除console
categories: vue
---

# 如何在打生产环境包时去除console信息

## 方法一、修改node_modules中的@vue包下关于terserOptions的配置

配置路径为node_modules/@vue/cli-service/lib/config/terserOptions.js

在compress对象中加上 drop_console: true 即可在打生产环境包时去除所有的 console。若不想删除所有的console信息，只是想删除console.log，则配置compress对象中的 pure_funcs 字段为 ["console.log"]。

## 方法二、在vue.config.js中直接修改webpack配置

既然核心方法是在webpack打包时利用terser插件实现去除console的效果，那么直接在vue.config.js中配置webpack即可

```javascript
// 引入terser插件
const TerserPlugin = require("terser-webpack-plugin");
// 使用configureWebpack字段配置webpack
module.exports = {
  // 下面两种配置方式选一即可
  // 一、配置式
  configureWebpack: {
    optimization: {
      minimizer: [
        new TerserPlugin({
          terserOptions: {
            compress: {
              pure_funcs: ["console.log"]
            }
          }
        })
      ]
    }
  },
  // 二、链式操作
  chainWebpack: config => {
    config.optimization.minimizer([new TerserPlugin({
      terserOptions: {
        compress: {
          pure_funcs: ["console.log"]
        }
      }
    })])
  }
};
```
