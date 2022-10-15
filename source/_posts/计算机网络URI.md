---
title: 计算机网络URI
date: 2020-05-03 15:58:54
tags:
  - URI
  - URL
  - URN
categories: 计算机网络
---

## URI

统一资源标识符（Uniform Resource Identifier），是用于唯一标识某一互联网资源的字符串。

## URL

统一资源定位符（Uniform Resource Locator），URL 是 URI 以定位方式的一种实现。

统一资源定位符的完整格式如下：
\[协议类型\]://\[访问资源需要的凭证信息\]@\[服务器地址\]:\[端口号\]/\[资源层级 UNIX 文件路径\]\[文件名\]?\[查询\]#\[片段 ID\]

其中\[访问凭证信息\]、\[端口号\]、\[查询\]、\[片段 ID\]都属于选填项。

例如：`http://www.example.com/index.html`

## URN

统一资源命名符（Uniform Resource Name），URN 是 URI 以命名方式的一种实现。

在旧的规范中，URN 相当于 URI，是一种规范性的定义。在新的规范中，URN 作为规范性定义的功能已被 URI 取代，URN 和 URL 则成为了 URI 的一种实现，特定情况下 URI 可能同时拥有 URN 与 URL。

URN 的格式：`urn:<NID>:<NSS>`，NID 为命名空间，NSS 为唯一名称

例如：`urn:issn:0167-6423`
