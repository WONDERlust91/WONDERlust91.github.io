---
title: typescript定义一个包含额外属性的函数并正确赋值
date: 2022-01-14 19:56:53
tags:
  - typescript
  - 函数属性
categories: typescript
---

在 javascript 中，我们要给函数表达式增加一个属性，直接如下增加即可：

```ts
const myFunction = () => {};
myFunction.prop = "some prop";
```

在 typescript 中，不定义类型，让函数表达式自动推断类型，仍然可以添加属性，上面的代码仍然不会报错。

但是，如果已经给函数表达式定义了类型，再添加属性就会报错：

```ts
const myFunction: () => void = () => {};
myFunction.prop = "some prop"; // Property 'prop' does not exist on type '() => void'.ts(2339)
```

在 typescript 并不支持在函数表达式中定义属性，如果我们要描述一个带有属性的可调用函数，只需要在对象类型中增加一个调用签名（call signature）：

注意 call signature 使用 `:` 分隔参数与返回值，而函数表达式使用 `=>`。

```ts
type MyFunction = {
  prop: string;
  (): void;
};
const myFunction: MyFunction = () => {};
myFunction.prop = "some prop";

// 另外也可以使用 Object.assign() 方法把声明函数与定义属性整合在一起
const myFunction: MyFunction = Object.assign(() => {}, {
  prop: "some prop",
});
```

> 参考资料
>
> [Call Signatures](https://www.typescriptlang.org/docs/handbook/2/functions.html#call-signatures)
>
> [Build a function object with properties in TypeScript](https://stackoverflow.com/questions/12766528/build-a-function-object-with-properties-in-typescript)
