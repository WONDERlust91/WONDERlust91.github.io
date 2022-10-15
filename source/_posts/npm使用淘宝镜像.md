---
title: npm使用淘宝镜像
date: 2018-10-20 23:08:12
tags: npm
categories: npm
---

由于国外镜像太慢，所以使用国内的淘宝npm镜像

方法一：命令行修改npm配置

更换registry为淘宝镜像地址

```bash
# 更换镜像
npm config set registry https://registry.npm.taobao.org/
# 检查更换是否成功
npm config get registry
```

方法二：直接修改npm配置文件

```bash
# 打开npm配置文件
npm config edit
```

将 registry=https://registry.npmjs.org/ 更换为 registry=https://registry.npm.taobao.org/

注意：将npm源更换为淘宝源后，npm search功能会报错，只能使用npm install功能。可以在使用npm search的时候手动临时使用官方源

```bash
npm search packageName --registry=https//registry.npmjs.org/
```