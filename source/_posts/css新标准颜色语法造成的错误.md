---
title: css新标准颜色语法造成的错误
date: 2021-08-01 20:50:05
tags:
  - css
  - color
categories: css
---

## css 新标准下的颜色语法

css 新标准下透明度也可写在 rgb 方法中，且可以不写逗号如`rgb(0 0 0 / 100%)`，该标准已被最新的浏览器实现，但尚未被 CSS 预处理器如 sass 和微信小程序实现，故暂时不推荐使用最新的写法，会造成预处理器编译错误和小程序白屏。

> 参考资料
>
> [Getting error in CSS with `rgb(0 0 0 / 15%)`](https://stackoverflow.com/questions/66825515/getting-error-in-css-with-rgb0-0-0-15)
>
> [rgb-functions](https://drafts.csswg.org/css-color/#rgb-functions)
