---
title: typescript中onchange事件类型
date: 2021-05-06 17:29:16
tags:
  - dom event
  - typescript
categories: typescript
---

在 React 框架中使用 Typescript 时，在遇到 DOM 事件时，总会遇到类型问题，最常见的就是 e.target.value 不存在。这时候就需要在回调函数中显式地声明事件类型。

```tsx
<Input
  onChange={(e: React.ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value);
  }}
/>
```

> [参考资料](https://stackoverflow.com/questions/40676343/typescript-input-onchange-event-target-value)
