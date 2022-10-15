---
title: css优先级之important
date: 2018-11-09 09:36:56
tags:
  - css
  - important
categories: css
---

注意：应该尽量使用 css 优先级规则，慎用!important，这会扰乱 css 的优先级规则，造成无法定位 bug 的情况。尽量使用优先级规则来解决问题，不要在全站 css 上使用!important，不要在插件中使用!important，只在特定页面使用。

可以使用的情况：有全站 css 文件时，还使用了在 html 中写入的内联样式，这时可以使用!important 覆盖内联样式。

若要覆盖!important 规则，只要在其后再加一条!important 规则。
