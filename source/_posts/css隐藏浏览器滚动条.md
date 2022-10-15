---
title: css隐藏浏览器滚动条
date: 2018-11-18 20:05:53
tags: 
- 隐藏滚动条
- css
categories: css
---
# css隐藏浏览器滚动条

## Chrome

```css
body::-webkit-scrollbar {
    display: none;
}
```

## IE/Edge

```css
body {
    -ms-overflow-style: none;
}
```