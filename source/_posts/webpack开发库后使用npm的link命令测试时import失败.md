---
title: webpack开发库后使用npm的link命令测试时import失败
date: 2021-06-04 11:23:22
tags:
  - webpack
  - npm
  - 库开发
categories: webpack
---

## 问题背景

使用 webpack 开发一个库后，想在未发布的条件下，对库进行测试，通常会使用`npm link`命令，该命令会将当前目录链接至用户文件夹下的 npm 包公用目录，在要引入包进行测试的项目中再使用`npm link 你已经链接至公用目录的包名`，就可以将库从公用目录再链接到测试的项目中，这就等同于使用 npm 直接安装包了。但是使用中发现还是有问题，使用`npm link`引入的包，在项目中 import 后会报错，但是同样的文件发布到 npm 后再 import 就没有问题。

## 解决方案

在测试项目中将 webpack 的符号链接配置为 false 即可，即可正确 import 要测试的库了

```js
// webpack配置
module.exports = {
  // ...
  resolve: {
    symlinks: false,
  },
};
```

<!-- more -->

## 原因分析

### symlinks 的作用

symlinks 表示是否将符号链接解析到它们的原始位置，symlinks 默认配置为 true，即相当于默认情况下，符号链接的资源（通过 npm link 引入的开发的库）解析为真实路径（开发的库的真实路径），而不是符号链接的位置（测试项目中的 node_modules 路径）。

### webpack 的打包机制

还记得你在配置库的 webpack 时，打包输出设置了什么吗？为了方便 CommonJS 和 AMD 方式引入，我们通常都会把`output.library.type`配置为`umd`，那么我们在项目中使用的时候却是使用 esm 的方式 import 的，为什么明明导出了 umd 却可以在项目中以 esm 的方式使用呢？

原来 webpack 会将 node_modules 下的所有文件事先经过一次编译，使 umd 形式的包符合 esm 的引用方式，而 webpack 默认`resolve.symlinks`配置为`true`，从库的真实路径去解析包了，并不是从项目的 node_modules 目录下去解析包的，因此 umd 形式的包未经过 webpack 编译为 esm 可引入的形式，故而会发生报错。这也是为什么经过 npm 发布后，同样的包就可以正确使用，而 npm link 却不行的原因。

P.S. webpack 对 esm 的支持还是试验性，需要进行额外的配置才能开启。

> 参考资料
>
> [记一次错：Vue 构建项目后使用 npm link 失败](https://www.jianshu.com/p/ca252cd667df)
>
> [webpack 官方配置文档 resolve-symlinks](https://webpack.docschina.org/configuration/resolve/#resolvesymlinks)
