---
title: npm执行install报错ENOENT没有文件或目录
date: 2021-05-20 11:03:48
tags: npm
categories: npm
---

遇到 npm 执行命令`npm install`时报错，ENOENT：No Such Files Or Directories...。删除 node_modules 目录也无法修复。

## ENOENT

ENOENT 是 Error NO ENTry（或 Error NO ENTity）的缩写，之所以这样缩写是因为 C 的编译器最初不支持超过 8 个字符的符号。

## 解决方案

可以同时删除 package-lock.json 与 node_modules 目录，再执行`npm install`。

> [参考资料 1](https://github.com/mapbox/node-sqlite3/issues/900)
>
> [参考资料 2](https://stackoverflow.com/questions/19902828/why-does-enoent-mean-no-such-file-or-directory#:~:text=It's%20an%20abbreviation%20of%20Error,than%208%20characters%20in%20symbols.)
