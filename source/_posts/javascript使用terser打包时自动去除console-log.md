---
title: javascript使用terser打包时自动去除console.log
date: 2019-08-12 13:43:15
tags:
- terser
- uglify
categories: javascript
---

## terser

由于老牌工具uglify不支持es6，且uglify-es不再更新，我们选择terser作为js代码压缩工具。

```shell
# 全局安装terser命令行工具
npm install -g terser
# 使用terser
terser ./foo.js -c pure_funcs=[console.log],toplevel=true -m -o bar.js
# -c即compress表示普通的压缩代码
# pure_funcs表示删除代码中的console.log方法
# toplevel为true表示只在顶级作用域压缩清理变量
# -m即mangle会压缩变量名等等
# -o代表输出路径
```

其他配置可以参考terser官网

## terser-webpack-plugin

这是terser的webpack插件版。由于vue-cli工具中已经用到了terser-webpack-plugin，因此在vue-cli新建的项目中可以直接引入terser-webpack-plugin，无需安装。

根据vue-cli3文档，所有对webpack的个性化配置都要写在vue.config.js中，示例如下：

```javascript
// vue.config.js
const TerserPlugin = require("terser-webpack-plugin");
module.exports = {
  configureWebpack: {
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
}
```
