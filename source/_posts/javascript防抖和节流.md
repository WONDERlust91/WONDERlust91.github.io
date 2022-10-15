---
title: javascript防抖和节流
date: 2021-09-22 16:28:19
tags:
  - 防抖（debounce）
  - 节流（throttle）
categories: javascript
---

## 防抖（debounce）

指在高频触发的事件中，延迟调用事件的回调函数直到规定的延迟时间内没有再次触发该事件，若在延迟时间内触发了该事件，则重置延迟时间。

通过防抖的定义，可以知道，防抖使事件的回调只执行了最后的一次。

```ts
// 简易实现
function debounce(callback: () => void, delay: number) {
  let timer: number | null = null;
  return () => {
    if (timer) {
      window.clearTimeout(timer);
    }
    timer = window.setTimeout(callback, delay);
  };
}
```

<!-- more -->

## 节流（throttle）

指在高频触发的事件中，回调函数在规定的时间内最多只触发一次。

可以看出节流与防抖都是减少高频事件对回调的触发，防抖只触发了一次，而节流触发了多次，只是降低了触发频率。

```ts
// 简易实现
function throttle(callback: () => void, delay: number) {
  let timer: number | null = null;
  return () => {
    if (timer !== null) return;
    timer = window.setTimeout(() => {
      callback();
      timer = null;
    }, delay);
  };
}
```

## 生产环境

上面的代码只是基于防抖与节流的核心概念提供了最基础的实现，生产环境使用时，还要考虑作用域、参数传递，首尾是否触发等等因素，因此生产环境推荐使用 lodash 库中的防抖、节流。

### lodash 防抖

```ts
_.debounce(func, [(wait = 0)], [(options = {})]);
```

参数

- func（Function）：需要进行防抖的函数

- [wait=0]（number）：延迟的毫秒数

- [options={}]（Object）：配置对象

- [options.leading=false]（boolean）：指定是否在延迟时间的开始调用函数，默认为 false

- [options.maxWait]（number）：函数调用的最大延迟时间

- [options.trailing=true]（boolean）：指定是否在延迟时间的结束调用函数，默认为 true

返回值

- （Function）：返回防抖后的函数

### lodash 节流

```ts
_.throttle(func, [(wait = 0)], [(options = {})]);
```

参数

- func（Function）：需要进行节流的函数

- [wait=0]（number）：限制调用的毫秒数

- [options={}]（Object）：配置对象

- [options.leading=true]（boolean）：指定是否在限制调用时间的开始调用函数，默认为 true

- [options.trailing=true]（boolean）：指定是否在限制调用时间的结束调用函数，默认为 true

返回值

- （Function）：返回节流后的函数

> 参考资料
>
> [Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)
>
> [浅谈 JS 防抖和节流](https://segmentfault.com/a/1190000018428170)
>
> [深入理解节流和防抖](https://bbs.huaweicloud.com/blogs/281408)
>
> [Lodash 官方文档](https://lodash.com/docs/4.17.15#debounce)
