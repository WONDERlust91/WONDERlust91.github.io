---
title: css子元素根据父元素绝对定位
date: 2018-10-21 23:12:01
tags: 
- css
- 绝对定位
- 子元素
categories: css
---

CSS中子元素position: absolute，会根据父元素定位，若父元素未指定position，父元素position则默认为static，子元素继续向上查找父元素position，以第一个找到position为非static的父级元素为绝对定位的基准，若直到body都没有找到position为非static的父级元素，则以body为绝对定位的基准。

结论：若要子元素根据父元素绝对定位，除了子元素的position设置为absolute以外，还要将父元素的position改为relative。
