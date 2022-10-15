---
title: css详解nth-child伪类选择器
date: 2021-09-10 18:09:09
tags:
  - nth-child
  - css伪类
categories: css
---

## 语法

`:nth-child(N)`

该伪类选择器匹配属于父元素的第 N 个子元素，即指选择器的第 N 个兄弟元素。

N 可以是整数、关键字或公式 an+b。

关键字为 odd（奇数）或 even（偶数）。

公式为 an+b，其中 n 为计数器，从 0 开始递增，a 与 b 是线性函数参数，可以是任意整数。

<!-- more -->

## 用法

### 数字

`span:nth-child(3)` 表示匹配所有 span 父元素的子元素中第 3 个且名字是 span 的元素，这里的 3 实际可以理解为 0n+3

### 关键字

`tr:nth-child(odd)` 表示表格中的奇数行

`tr:nth-child(even)` 表示表格中的偶数行

### 公式

`tr:nth-child(2n+1)` 表示表格中的奇数行

`tr:nth-child(2n)` 表示表格中的偶数行

`tr:nth-child(-n+3)` 表示表格中的前三行，因为随着 n 增大，取值为 3，2，1，取值不可小于 1，故最终为前 3 个。

> 参考资料
>
> [MDN-:nth-child](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:nth-child)
>
> [CSS3 :nth-child()伪类选择器](https://www.huaweicloud.com/articles/b5cbccd24d422b062ff05eb7090cbd1a.html)
