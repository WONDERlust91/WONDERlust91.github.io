---
title: html的span标签无法设置宽与高
date: 2018-12-06 10:09:45
tags:
- html
- span
categories: html
---

# span标签无法设置宽和高

为什么在给span标签设置了宽和高后无法生效？因为span标签是内联元素，没有固定的格式表现，在浏览器标准中span标签的width与height属性会被忽略，故无法设置宽和高。

我们可以给span标签设置display属性为inline-block，使span标签成为行内块级元素，这样就可以改变span标签的宽和高了。