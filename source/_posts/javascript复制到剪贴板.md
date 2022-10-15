---
title: javascript复制到剪贴板
date: 2019-08-05 10:09:06
tags:
- 剪贴板
- clipboard
categories: javascript
---

## 常用方法

1. 第三方库：clipboard.js
2. 原生api：Clipboard API（还处于草案阶段，兼容性差）
3. document.execCommand()

若是项目中需要经常操作剪贴板，推荐使用第三方库，若仅是轻量使用，推荐document.exeCommand()方法。待原生api稳定后，使用原生api是更为优雅的方式。

这里我们着重讲document.execCommand()方法

## document.execCommand()

首先需要注意的是document.execCommand()方法只能操作可编辑区域的内容，如input、textarea等，像div这类无法聚焦的元素原则上是无法使用document.execCommand()方法的，在div上增加tabIndex属性，让div变成一个可聚焦可编辑元素，这里仅提供一个思路，不做详细展开。

我们使用textarea做为可编辑区域，在文档对象中新建一个textarea元素，然后挂载到文档末尾，再将要获取的文本赋值给textarea，最后通过document.execCommand()方法获取textarea中选中的值并删除这个textarea元素。

```javascript
let textarea = document.createElement("textarea");
document.body.appendChild(textarea);
textarea.value  = "test string";
textarea.select();
document.execCommand("copy");
document.body.removeChild(textarea);
```

> 参考链接 [JavaScript复制内容到剪贴板](https://juejin.im/post/5a94f8eff265da4e9b593c29)
