---
title: scss与less主题切换对比
date: 2021-05-25 21:06:23
tags:
  - scss
  - less
  - 主题切换
categories: scss
---

## less 实现主题切换的语法

```less
// 设置主题模板函数
.common-theme(@backgroundColor, @color) {
  .class__A {
    background-color: @backgroundColor;
    color: @color;
  }
}

// 实现主题-亮色
.theme-light {
  .common-theme(#fff, #d9d9d9);
}
// 实现主题-暗色
.theme-dark {
  .common-theme(#000, #fff);
}
```

## scss 实现主题切换的语法

### 一、遍历配置

```scss
// 主题配置
$theme-config: (
  light: (
    backgroundColor: #fff; color: #d9d9d9;,
  ),
  dark: (
    backgroundColor: #000; color: #fff;,
  ),
);
@each $theme, $config in $theme-config {
  // 属性名使用变量时需要使用#{}包裹
  .theme-#{$theme} {
    .class__A {
      background-color: map-get($config, "backgroundColor");
      color: map-get($config, "color");
    }
  }
}
```

### 二、mixin

```scss
@mixin common-theme($backgroundColor, $color) {
  .class__A {
    background-color: $backgroundColor;
    color: $color;
  }
}
// 亮色
.theme-light {
  @include common-theme(#fff, #d9d9d9);
}
// 暗色
.theme-dark {
  @include common-theme(#000, #fff);
}
```

> 参考资料-[less、scss 主题切换](https://blog.csdn.net/bocongbo/article/details/104773028)
