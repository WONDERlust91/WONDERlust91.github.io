---
title: typescript全局类型声明问题
date: 2021-05-03 20:34:09
tags:
  - typescript
  - 全局类型
categories: typescript
---

## d.ts 中使用 import 语法，导致全局类型失效问题

一个常见的应用场景，即我们需要扩展 window 全局变量，但 window 的类型声明不包括我们新增的属性，故会通过在全局的 d.ts 文件中书写 window 的 interface 来给 window 增加新的属性，当新属性的类型引用到某个导出的类型时，就要使用 import 语法导入这个类型，一旦使用 import 语法，d.ts 文件就从默认全局转为了默认模块。如何解决全局类型失效的问题呢？

使用 global 的 namespace 即可

```ts
// 使用三斜线语法引入类型，该d.ts文件还是全局，但这个文件本身引用不到/ref-path下的类型
/// <reference path="/ref-path">
interface window {
  prop: MyType; // Error 虽然MyType在/ref-path下，但该文件中还是读取不到
}

// 使用import语法引入类型，该d.ts文件默认转为模块，需要使用global命名空间来使某些声明成为全局
import { MyType } from "/ref-path";
declare global {
  interface window {
    prop: MyType;
  }
}
```

> 参考资料
>
> [\*.d.ts 导入 import 其它类型导致全局类型失效问题](https://blog.csdn.net/u013727805/article/details/114758672)
>
> [Typescript 入门教程](https://ts.xcatliu.com/basics/declaration-files.html)
