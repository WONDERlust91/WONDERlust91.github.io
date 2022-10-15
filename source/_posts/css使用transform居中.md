---
title: css使用transform居中
date: 2018-11-09 09:11:40
tags: 
- css
- 居中
- transform
categories: css
---
# 使用transform居中

绝对定位时尽量使用transform中的translate进行定位，而不是用margin为负值进行定位，因为margin为负值的定位是必须要知道自身的宽高的，而transform中的translate可以用-50%这样的百分比。

```css
/* 父元素 */
.parent {
    position: relative;
}
/* 子元素 */
.child {
    position: absolute;
    /* 水平居中 */
    left: 50%;
    transform: translateX(-50%);
    /* 垂直居中 */
    top: 50%;
    transform: translateY(-50%);
    /* 可以合并写为 */
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}
```