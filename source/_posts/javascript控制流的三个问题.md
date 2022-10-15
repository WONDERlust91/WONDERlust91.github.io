---
title: javascript控制流的三个问题
date: 2018-02-26 21:55:35
tags:
  - 控制流
  - for
  - switch
categories: javascript
---

## switch 的特性

switch 语句会从找到的第一个匹配分支起执行语句，如果没有遇到 break, return, continue, throw 等控制语句，会一直执行到 switch 语句的最后，此时，可能已经贯穿执行了其他 case 条件下的语句。这可以做为不同 case 使用相同执行语句的方法，减少代码量。但这也可能会带来问题。所以应小心这种 fall-through execution（贯穿执行）的问题。

<!-- more -->

```javascript
switch (abc) {
  case 1:
    abc - 1;
    break;
  case 2:
    abc - 2;
    break;
  case 3: // 如果case3和case4都执行相同代码，则可以利用fall-through execution，写为
    abc - 3;
    break; // case 3:
  case 4: // case 4:
    abc - 3; //     abc - 3;
    break; //     break;
}
```

## for...in 与 for...of 循环

for...in 循环遍历得是键名，所以一般不用于遍历数组，且 for...in 循环会遍历到原型方法和属性。

```javascript
const player = { name: "Thomas", rank: "Midshipman", age: 25 };
Object.prototype.abcde = "fghijk"; // 添加原型属性
const playerArr = ["Thomas", "Midshipman", 25];
Array.prototype.abcde = "fghijk"; // 添加原型属性
for (let prop in player) {
    if(!player.hasOwnProperty(prop)) continue; // 防止输出原型属性
    console.log(prop + ": " + player[prop]);
}
for (let prop in playerArr) {
    if(!playerArr.hasOwnProperty(prop)) continue; // 防止输出原型属性
    console.log(prop + ": " + playerArr[prop]);
}
/*
有if语句时输出为

name: Thomas
rank: Midshipman
age: 25

0: Thomas
1: Midshipman
2: 25

无if语句时输出为

name: Thomas
rank: Midshipman
age: 25
abcde: fghijk

0: Thomas
1: Midshipman
2: 25
abcde: fghijk
```

for...of 循环遍历得是元素（值）而非索引（键），对象通常被 for...of 循环认为是不可迭代的，故 foo...of 循环一般用于遍历数组与类数组。

```javascript
const playerArr = ['Thomas', 'Midshipman', 25];
Array.prototype.abcde = 'fghijk'; // 添加原型属性
for (let prop of playerArr) {
  //if(!playerArr.hasOwnProperty(prop)) continue; // 无防止输出原型属性的语句也不会输出原型
  console.log(prop);
}
// 输出结果为
// Thomas
// Midshipman
// 25
```

## 列表变动时索引递减

如果列表有增删操作，使用索引递减来代替索引递增，就不会影响循环终止条件。
索引递减是循环顺序由终止的地方开始，到起始位置结束。

```javascript
// 本例目的为将数组中能被2和3整除的数都从数组中剔除
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
for (let i = 0; i < arr.length; i++) {
  if (arr[i] % 2 === 0 || arr[i] % 3 === 0) arr.splice(i, 1);
} // 使用索引递增删除被2整除的数后，下一个数因为数组长度和索引的改变就被跳过了，3、5、7、9都被跳过了。
console.log(arr);
// [1, 3, 5, 7, 9]
const arr1 = [1, 2, 3, 4, 5, 6, 7, 8, 9];
for (let i = arr1.length - 1; i >= 0; i--) {
  if (arr1[i] % 2 === 0 || arr1[i] % 3 === 0) arr1.splice(i, 1);
} // 使用索引递减，数组更改后，未被处理的数索引不变，只有已经被处理过的数索引改变了，故不会发生跳过的问题。
console.log(arr1);
// [1, 5, 7]
```
