---
title: scss学习笔记
date: 2018-10-21 23:35:29
tags: scss
categories: scss
---

&符号代表当前元素

```scss
#main {
    color: black;
    a {
        font-weight: bold;
        &:hover {
            color: red;
        }
    }
}
// is compiled to
#main {
    color: black;
}
#main a {
    font-weight: bold;
}
#main a:hover {
    color: red;
}
```
