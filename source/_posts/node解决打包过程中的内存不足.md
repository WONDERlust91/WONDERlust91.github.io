---
title: node解决打包过程中的内存不足
date: 2022-02-10 13:34:39
tags:
  - out of memory
  - 打包
categories: node
---

## 问题描述

在打包大的项目时，打包过程中会报错失败。报错信息为 `FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory in XXX`。

首先一定要排除代码中是否有会导致内存泄露的可能，如果没有内存泄露，项目又比较大，打包过程确实消耗较大的内存，那么请继续阅读。

查阅资料后发现，当电脑内存为 8G 时，安装 node，默认分配给堆的内存大小就是 2G，一旦项目运行时超过这个限制就会报错。可以使用如下命令来查看自己的堆大小限制：

```shell
node -e 'console.log(v8.getHeapStatistics().heap_size_limit/(1024*1024))'
```

<!-- more -->

## 解决方案

既然是分配给堆的内存大小不够造成的问题，那么在打包时将堆的内存大小设置大一些即可。

### windows 环境下使用 set 关键字设置

```shell
set NODE_OPTIONS=--max-old-space-size=4096 && npm run build
```

### linux 或 macOS 环境下使用 export 关键字设置

```shell
export NODE_OPTIONS=--max-old-space-size=4096 && npm run build
```

## 永久设置

上述对内存大小的设置，都仅在当前执行环境生效，下次还需要重新设置。如何实现永久化设置？

### windows 环境

需要通过 我的电脑 - 属性 - 高级系统设置 - 环境变量 来配置。

### linux 或 macOS 环境

- 需要修改系统文件，修改所有用户或当前用户皆可。

```shell
# 所有用户
/etc/profile
# 当前用户
~/.bash_profile
```

- 打开文件，并在文件末尾添加对环境变量的修改

```shell
export NODE_OPTIONS=--max-old-space-size=4096
```

- 修改完成后，重新加载系统文件

```shell
# 所有用户
source /etc/profile
# 当前用户
source ~/.bash_profile
```

## 跨端设置

修改环境变量，基本上解决了个人的问题，但基于 DEVOPS 的构建环境，打包动作并不是只在本机运行，那么就会遇到新的问题：CI/CD 服务器也可能出现内存资源不足的情况，需要在服务器环境自动构建时，也设置环境变量。

解决方案：把设置环境变量的操作写入 package.json 的 scripts 中。

```json
// package.json
// 修改前
{
  ...
  "scripts": {
    "build": "craco build"
  }
}
// 修改后
{
  ...
  "scripts": {
    "build": "set NODE_OPTIONS=--max-old-space-size=4096 && craco build"
  }
}
```

如果本机环境为 windows，CI/CD 服务器的环境为 linux，这时你会发现自动构建还是会报错，因为 linux 系统并不支持 set 关键字，而需要使用 export 关键字。如何解决这个跨系统的问题呢？

网上大量的文章推荐了 cross-env 这个库，当通过 npm 安装这个库后，并修改命令：

```shell
npm i -D cross-env
```

```json
{
  ...
  "scripts": "cross-env NODE_OPTIONS=--max-old-space-size=4096 && craco build"
}
```

你会发现并没有效果。因为 cross-env 并不支持串行命令，环境变量只影响到了 `&&` 前的执行环境，`&&` 后的 `craco build` 并没有修改到环境变量。

我最终选择了 [cross-os](https://www.npmjs.com/package/cross-os) 这个库，在不同的环境下，执行不同的命令。

```shell
# 安装
npm i -D cross-os
```

```json
// 配置
{
  ...
  "scripts": {
    "build": "cross-os memory-build",
  },
  "cross-os": {
    "memory-build": {
      "win32": "set NODE_OPTIONS=--max_old_space_size=4096 && craco build", // windows 环境
      "darwin": "export NODE_OPTIONS=--max_old_space_size=4096 && craco build", // macOS 环境
      "linux": "export NODE_OPTIONS=--max_old_space_size=4096 && craco build" // linux 环境
    }
  }
}
```
  
> 参考资料
>
> [FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory in Ionic 3](https://stackoverflow.com/questions/53230823/fatal-error-ineffective-mark-compacts-near-heap-limit-allocation-failed-javas)
>
> [环境变量](https://www.cnblogs.com/sorex/p/6200940.html)
>
> [Node 环境变量设置](https://yi-jy.com/2018/08/08/node-env/)
>
> [cross-os](https://www.npmjs.com/package/cross-os)
