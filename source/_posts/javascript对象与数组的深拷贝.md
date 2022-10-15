---
title: javascript对象与数组的深拷贝
date: 2019-07-12 18:24:45
tags:
- 对象
- 数组
- 深拷贝
categories: javascript
---

## 对象与数组的特点

对象与数组都是与字符串、数字、布尔等基本类型不同的类型，当我们将一个对象或数组赋值给另一个变量时，我们并没有复制这个对象或数组，而是将原对象或原数组在内存位置中的索引赋值给了这个变量，这就导致我们在修改这个变量时，最初的对象或数组也被改变了。因为我们通过变量中的内存位置索引，找到的就是最初的对象或数组本身。

## 对象与数组的浅拷贝

当我们需要复制一个对象或数组，而不是仅仅获得当前对象或数组的索引时，就要用到对象或数组的拷贝。对象的拷贝使用Object.assign方法，Object.assign(target, ...sources)，target为目标对象，sources为源对象，源对象可以是一个或多个。数组的拷贝使用Array.from方法，Array.from(arrayLike[, mapFn[, thisArg]])，arrayLike是要转换成数组的伪数组对象或可迭代对象；mapFn可选参数，代表新数组中每个元素会执行的回调函数；thisArg可选参数，执行回调函数mapFn时的this对象。

```javascript
const foo = {
  a: 1,
  b: 2
};
const newFoo = Object.assign({}, foo);
const bar = [1, 2];
const newBar = Array.from(bar);
```

## 对象与数组的深拷贝

当对象或数组中嵌套了对象或数组时，对象与数组的浅拷贝无法将嵌套的对象或数组拷贝进去，得到的还是原对象或原数组内存位置的索引。这个时候就要对对象或数组进行深拷贝了。深拷贝需要递归对象或数组进行拷贝，代码如下：

```javascript
export const deepCopyObject = obj => {
  let newObj;
  // 判断对象是否为数组
  Array.isArray(obj) ? (newObj = []) : (newObj = {});
  // 获取对象可迭代的键值并遍历
  Object.keys(obj).forEach(key => {
    // 若对象内的值还是一个对象，则递归；否则直接赋值
    if (typeof obj[key] == "object") {
      newObj[key] = deepCopyObject(obj[key]);
    } else {
      newObj[key] = obj[key];
    }
  });
  return newObj;
}
```
