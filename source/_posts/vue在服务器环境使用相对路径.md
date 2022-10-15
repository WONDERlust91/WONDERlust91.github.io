---
title: vue在服务器环境使用相对路径
date: 2018-11-08 23:21:51
tags:
  - vue
  - 生产环境
  - 相对路径
categories: vue
---

# vue 使用相对路径

当服务器下不单单运行一个项目时，根目录下会有多个项目，vue 使用默认的根路径打包项目时，项目中文件的依赖会去根目录下查找，而出现找不到当前路径下所依赖的 js 与 css 文件的情况，故应在 vue.config.js 中将默认的基础路径设置为相对路径，即可在多项目服务器下正确运行了。

```javascript
module.export = {
  // 默认的baseUrl为"/"
  baseUrl: "./"
};
```

## **切记！！！** vue history 模式不要使用相对路径的 baseUrl

> ## 相对 baseUrl 的限制
>
> 相对路径的 baseUrl 有一些使用上的限制。在以下情况下，应当避免使用相对 baseUrl:
>
> 1. 当使用基于 HTML5 history.pushState 的路由时；
> 2. 当使用 pages 选项构建多页面应用时。

history 模式要对服务器进行配置，详见[官方文档](https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90)

## 2019 年 3 月 8 日更新

由于项目几乎都不会放在根目录下，而且项目都需要使用 history 模式，故无法在基础路径中使用相对路径，因此应该尽量约定好项目在服务器上的目录名，并在基础路径上以绝对路径方式配置。注意，vue-cli 3.3 版本后使用 publicPath 替代了原来 vue.config.js 中 baseUrl 的配置。

```javascript
module.exports = {
  publicPath: process.env.NODE_ENV === 'production'
    ? '/production-sub-path/'
    : '/'
}
```
