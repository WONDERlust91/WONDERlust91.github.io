---
title: react在typescript下传递ref给子组件
date: 2021-05-19 11:05:15
tags:
  - react
  - typescript
  - forwardRef
  - Ref
  - ElementRef
categories: react
---

## 应用场景

在做可复用组件时，我们常常需要给函数组件传递 ref 属性，以访问或操作组件内部的 DOM 或向外暴露方法等。

```ts
import { useRef } from "react";

// 组件
function Component() {
  return <input />;
}

// App
function App() {
  const textInputRef = useRef();
  return <Component ref={textInputRef} />; // 这是无效的
}
```

## 在 jsx 下使用

默认情况下，我们不能在函数组件上使用 ref 属性，因为它们没有实例。

如果要在函数组件中使用 ref，可以使用 forwardRef 包裹组件函数使用（可与 useImperativeHandle 结合使用）。

被 forwardRef 包裹的组件函数除 props，还要多传入第二个参数：ref，即从外部传入的 ref。

useImperativeHandle 接收三个参数，第一个是 forwardRef 传入的 ref；第二个参数是 handle 函数，返回要向外暴露的值，通常是对象；第三个参数是依赖数组，根据依赖变化执行更新，非必需参数。

```ts
import { useRef, forwardRef, useImperativeHandle, useEffect } from "react";

// 组件1
const Component1 = forwardRef((props, ref) => <input ref={ref} />);
// 组件2
const Component2 = forwardRef((props, ref) => {
  // ref 为第一个参数，返回带有 sayHello 方法对象的函数为第二个参数
  useImperativeHandle(ref, () => {
    return {
      sayHello: () => console.log("hello"),
    };
  });
  return <div>Say hello in console.log</div>;
});

// App
function App() {
  const textInputRef = useRef();
  const helloRef = useRef();
  useEffect(() => {
    // 聚焦 textInputRef
    textInputRef.current.focus();
    // 调用 helloRef 的 sayHello 方法
    helloRef.current.sayHello();
  }, []);
  return (
    <>
      <Component1 ref={textInputRef} />
      <Component2 ref={helloRef} />
    </>
  );
}
```

## 在 tsx 下使用

以上是在 React 中使用 forwardRef 的情况，那么结合了 Typescript，又会发生很多的类型问题，如何在 TS 中使用 forwardRef 呢？

```ts
import { useRef, forwardRef, Ref, useImperativeHandle, ElementRef, useEffect } from "react";

// 组件，有两种定义类型的方式

// 组件1，一种是使用 forwardRef 的泛型
// forwardRef 泛型第一个参数是 Ref 类型，
// 第二个参数是 props 类型，不传时默认类型为{}，
// 注意，forwardRef 泛型与内部包裹函数的参数顺序恰恰相反，易造成歧义
const Component1 = forwardRef<HTMLInputElement, {}>((props, ref) => <input ref={ref} />);

// 组件2，另一种是是在函数参数上直接定义类型
// 注意，在函数参数上定义类型时，ref 参数类型需要使用Ref泛型包裹，而 forwardRef 泛型则不需要
const Component2 = forwardRef(
  // ref 类型使用了 Ref 泛型包裹
  (props: {}, ref: Ref<{ sayHello: () => void }>) => {
    // ref 为第一个参数，返回带有 sayHello 方法对象的函数为第二个参数
    useImperativeHandle(ref, () => {
      return {
        sayHello: () => console.log("hello"),
      };
    });
    return <div>Say hello in console.log</div>;
  }
);

// App
export default function App() {
  // 在父组件中使用时需要使用 ElementRef 泛型，并使用 typeof 获取组件的 ref 类型
  const textInputRef = useRef<ElementRef<typeof Component1>>(null);
  const helloRef = useRef<ElementRef<typeof Component2>>(null);
  useEffect(() => {
    // 聚焦textInputRef
    if (textInputRef.current) textInputRef.current.focus();
    // 调用helloRef的sayHello方法
    if (helloRef.current) helloRef.current.sayHello();
  }, []);
  return (
    <>
      <Component1 ref={textInputRef} />
      <Component2 ref={helloRef} />
    </>
  );
}
```

可以看到，有两种方式定义 forwardRef 组件的类型。

- 一种是在 forwardRef 泛型中定义 ref 类型与 props 类型，需要注意的是泛型中是 ref 类型在前，props 类型在后，而函数组件中是 props 在前，ref 在后。

- 另一种则是直接在函数组件的参数中定义类型，需要注意的是这种方式定义 ref 类型需要使用 Ref 泛型。

另外在父组件中，使用 useRef 时，则需要通过 ElementRef 泛型与 typeof 结合获取 ref 类型。

forwardRef 泛型中，若组件没有用到 props，则 props 类型可不传，默认为{}。示例如下：

```ts
const Component = forwardRef<HTMLInputElement>((props, ref) => <input ref={ref}>);
```

> 参考资料
>
> [declare type with React.useImperativeHandle()](https://stackoverflow.com/questions/62210286/declare-type-with-react-useimperativehandle)
>
> [@types/react - github](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react/v16/index.d.ts)
>
> [Forwarding React Refs with TypeScript](https://www.carlrippon.com/react-forwardref-typescript/)
