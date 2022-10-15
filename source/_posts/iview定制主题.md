---
title: iview定制主题
date: 2018-11-08 23:00:18
tags:
  - iview
  - 自定义主题
categories: iview
---

如果你的项目使用了 webpack 工程，可以通过变量覆盖的方式来实现主题定制。首先在项目中先建一个目录，比如 my-theme，然后在 my-theme 下建立一个 less 文件 index.less，并写入下面内容：

```CSS
@import '~iview/src/styles/index.less';
/* 例子 */
/* 完整的变量列表可以查看 默认样式变量。 */
@primary-color: #8c0776;
```

然后在入口文件 main.js 内导入这个 less 文件即可：

```javascript
import Vue from "vue";
import iView from "iview";
import "../my-theme/index.less";
Vue.use(iView);
```

注意: 要在项目中使用 npm 安装 less 与 less-loader 否则运行和打包时都会报错，在 vue 新的版本中可能安装 less 和 less-loader 后还会报 inline javascript is not enabled. 可以在 vue.config.js 配置文件中写入如下配置来解决：

```javascript
module.exports = {
  css: {
    loaderOptions: {
      less: {
        javascriptEnabled: true,
      },
    },
  },
};
```
