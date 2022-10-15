---
title: 手机googlePlay安装指南
date: 2020-05-31 23:03:35
tags:
  - 手机
  - google play
categories: 手机
---

## google play 安装

前置条件：科学上网

### 1. 在[apkmirror](https://apkmirror.com)网站上下载 Google Play Service 与 Google Play Store 安装包

### 2. 安装成功后在科学上网的环境下使用

## 故障排除

1. 安装完成后，无法登陆（确保已科学上网）。报错信息为Couldn't sign in, There was a problem communicating with Google servers. Try again later.

2. 已登陆但报错：从服务器检索信息时出错。DF-DFERH-01。

3. 明明已科学上网且登陆成功，但经常无法访问Google 服务。

这些问题都是同一个原因引起的：国产手机系统默认将Google Play Service的请求地址更改为googleapis.cn，而google并未在大陆提供服务，故无法使用，默认状态下，需要很久才能判定googleapis.cn响应超时，转而去请求googleapis.com。可通过root权限修改手机host文件，将谷歌服务框架的请求修改为googleapis.com，或在科学上网工具中加入一条规则，使googleapis.cn重定向到googleapis.com来解决问题。也可以通过请求googleapis.cn时断开网络再重新连接，使对googleapis.cn的请求直接判定为失败，从而直接重定向到googleapis.com上。

上面的办法都是些治标不治本的法子，建议买外国手机。。。
