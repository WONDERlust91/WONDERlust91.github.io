---
title: typescript联合类型无法用于对象的索引签名参数
date: 2022-02-09 16:16:39
tags:
  - 联合类型
  - union type
  - 对象索引签名参数
  - index signature parameter
categories: typescript
---

## 问题描述

声明了一个联合类型，想要将联合类型作为对象的 key，但是报错。

```ts
type TestUnionType = "foo" | "bar";

interface TestInterface {
  [index: TestUnionType]: any;
}

// error message
// An index signature parameter type cannot be a union type. Consider using a mapped object type instead.
```

## 解决方案

通过报错信息可以看出，联合类型不能作为对象索引签名参数，而应该使用映射对象类型。

```ts
type TestUnionType = "foo" | "bar";

interface TestInterface {
  [index in TestUnionType]: any;
}
```

> 参考资料
>
> [Quick fix for 'unions can't be used in index signatures, use a mapped object type instead'](https://github.com/microsoft/TypeScript/issues/24220)
>
> [Mapped Types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)
