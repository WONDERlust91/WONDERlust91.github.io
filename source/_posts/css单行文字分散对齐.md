---
title: css单行文字分散对齐
date: 2019-03-08 13:17:23
tags:
- 分散对齐
- 两端对齐
categories: css
---

# css单行文字分散对齐

在页面中，我们有时候为了显示上的美观，会做文字的分散对齐，如何用css实现单行文字分散对齐呢？

```css
/* webkit内核浏览器 */
.test {
  text-align-last: justify;
}
/* IE浏览器兼容方案 */
.test {
  text-align: justify;
  text-justify: distribute;
}
```