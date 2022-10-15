---
title: css百分比表格宽度超出文本省略
date: 2018-11-09 09:57:21
tags: 
- css
- table
- td百分比
- 超出文本不换行省略
categories: css
---
# 文本超出宽度后不换行，隐藏后显示省略号

这是众所周知的三行代码

```css
td {
    /* 文本不换行 */
    white-space: nowrap;
    /* 超出大小隐藏 */
    overflow: hidden;
    /* 文本超出显示省略号 */
    text-overflow: ellipsis;
}
```

## 表格百分比宽度时无效

但是当表格td宽度设为百分比时，这三行代码并没有生效，表格还是被撑开了。当表格宽度使用像素px时，这三行代码又可以生效，如何解决这个问题呢？

在表格table上加一个样式，table-layout: fixed就可以解决了。

```css
table {
    table-layout: fixed;
}
```

table-layout设定为fixed将自动表格布局算法变为了固定表格布局算法，此时布局渲染更快，表格布局只与设定的宽度与间距有关，与内容无关。自动表格布局刚好相反。

> 参考链接
> [https://blog.csdn.net/Altaba/article/details/55805586](https://blog.csdn.net/Altaba/article/details/55805586)