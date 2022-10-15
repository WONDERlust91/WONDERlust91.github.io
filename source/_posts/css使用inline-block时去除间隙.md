---
title: css使用inline-block时去除间隙
date: 2019-05-13 13:16:06
tags: inline-block
categories: css
---

## 问题描述

属性为display: inline-block的元素之间，换行显示或用空格分隔的情况下会产生间距。

```html
<ul>
  <li>一</li>
  <li>二</li>
  <li>三</li>
  <li>四</li>
</ul>
```

```css
ul li {
  display: inline-block;
}
```

这种间距会影响到我们布局，如何去除这种间距？

## 经典解决方案

### 使用html解决，将inline-block元素写在一行里，或让标签首尾不换行

```html
<!-- 将inline-block元素写在一行 -->
<ul>
  <li>一</li><li>二</li><li>三</li><li>四</li>
</ul>
<!-- 标签首尾不换行 -->
<ul>
  <li>
  一</li><li>
  二</li><li>
  三</li><li>
  四
  </li>
</ul>
```

缺点：影响代码美观与格式化

### 使用css解决，设置父元素font-size为0，或设置子元素margin或letter-spacing或word-spacing属性为负值

```css
/* 设置font-size */
ul {
  font-size: 0;
}
ul li {
  font-size: 14px;
}
/* 设置margin或letter-spacing或word-spacing */
ul li {
  margin: -1;
}
ul li {
  letter-spacing: -1;
}
ul li {
  word-spacing: -1;
}
```

缺点：跨浏览器兼容不好

## 终级解决方案

### 在父元素上使用display: flex

```css
ul {
  display: flex;
}
ul li {
  display: inline-block;
}
```
