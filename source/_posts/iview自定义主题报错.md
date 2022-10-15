---
title: iview自定义主题报错
date: 2018-10-21 23:24:31
tags:
    - iview
    - 自定义主题
categories: iview
---

# iview 自定义主题报错

首先确认安装了 less 和 less-loader，如果仍然报错，且报错为 Inline Javascript is not enabled. Is it set in your option? 这说明 less-loader 的配置没有开启行内使用 Js。应该在 vue.config.js 中设置，由于 vue-cli3.0 后，vue.config.js 不再默认出现在文件目录中，所以要手动新建该文件。然后在文件中设置开启 inline Javascript 如下：

```javascript
module.exports = {
    css: {
        loaderOptions: {
            // 向 CSS 相关的 loader 传递选项
            less: {
                javascriptEnabled: true
            }
        }
    }
};
```

若是 vue2.X 版本则应该在 webpack.base.config.js 中配置如下：

```javascript
{ loader: 'less-loader', options: { javascriptEnabled: true } }
```
