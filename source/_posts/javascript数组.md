---
title: javascript数组
date: 2018-03-29 20:13:54
tags: 数组
categories: javascript
---
数组是有序的，其索引从0开始。javascript中的数组可以是非齐次的，也就是说，数组中的元素可以是不同类型。（数组的元素可以是另一个数组或是对象）
# 数组的基本操作
## 在起始与末尾添加删除元素
1. arr.push(element)添加一个元素至数组末尾，返回值为添加元素后数组的长度
2. arr.pop()删除数组末尾最后一个元素，返回值是被删除的元素
3. arr.unshift(element)添加一个元素至数组开头，返回值为添加元素后数组的长度
4. arr.shift()删除数组开头第一个元素，返回值是被删除的元素

这4个方法会直接修改原数组。

```javascript
const arr = ["b", "c", "d"];
arr.push("e"); // 返回4，arr为["b", "c", "d", "e"]
arr.pop(); // 返回"e"，arr为["b", "c", "d"]
arr.unshift("a"); // 返回4，arr为["a", "b", "c", "d"]
arr.shift(); // 返回"a"，arr为["b", "c", "d"]
```
## 串接
arr.concat(anotherArr | elements)

该方法若将数组作为参数传入则将数组拆分为元素并添加至调用该方法的数组末尾，若将多个元素作为参数传入则直接将元素添加至调用该方法的数组末尾。concat方法只会拆分最外层直接传入的数组，并不会拆分数组内的数组。

这个方法不会修改原数组，会返回一个新的数组。
```javascript
const arr = [1, 2, 3];
arr.concat([4, 5, 6]); // 返回[1, 2, 3, 4, 5, 6]，arr不变
arr.concat(4, 5, 6); // 返回[1, 2, 3, 4 5, 6]，arr不变
arr.concat([4, 5], 6); // 返回[1, 2, 3, 4, 5, 6]，arr不变
arr.concat([4, [5, 6]]); // 返回[1, 2, 3, 4, [5, 6]]，arr不变
```
<!-- more -->
## 切分
arr.slice([begin[, end]])

该方法返回一个从开始索引到结束索引（不包括结束索引元素）的新数组，begin默认值为0，end默认值为arr.length，两个参数都省略，则从0索引到arr.length结束。两个参数均可为负，负值表示从末尾倒数的索引。

这个方法不修改原数组，返回一个新的数组。
```javascript
const arr = [1, 2, 3, 4, 5];
arr.slice(); // 返回[1, 2, 3, 4, 5]，arr不变
arr.slice(2); // 返回[3, 4, 5]，arr不变
arr.slice(2, 4); // 返回[3, 4]，arr不变
arr.slice(-2); // 返回[4, 5]，arr不变
arr.slice(-2, -1) // 返回[4]，arr不变
```
## 拼接
arr.splice(beginIndex[, deleteCount[, element1, element2, ...]])

该方法通过删除、添加元素来改变数组。beginIndex参数必填，指定了修改的起始位置索引，deleteCount指定删除元素的个数，为0时不删除，省略时由beginIndex开始直至末尾全部删除，element为要添加的元素，只删除时，可省略。

这个方法返回一个由被删除的元素组成的数组。若只删除了一个元素，则返回只有一个元素的数组。若没有删除元素，则返回空数组。这个方法会改变原数组。
```javascript
const arr = [1, 5, 7];
arr.splice(1, 0, 2, 3, 4); // 返回[]，arr为[1, 2, 3, 4, 5, 7]
arr.splice(5, 0, 6); // 返回[]，arr为[1, 2, 3, 4, 5, 6, 7]
arr.splice(2, 2); // 返回[3, 4]，arr为[1, 2, 5, 6, 7]
arr.splice(1, 1, "b", "c", "d"); // 返回[2]，arr为[1, "b", "c", "d", 5, 6, 7]
arr.splice(2); // 返回["c", "d", 5, 6, 7]，arr为[1, "b"]
```
## 内部复制
arr.copyWithin(target[, start[, end]]) ES6新方法

该方法复制数组自身的一部分到指定的自身目标位置。target为目标索引，代表了复制序列到该位置，且必须指定。当目标索引超过数组长度时，不进行复制。当复制序列长度超过目标索引到数组结束的长度时，复制序列只复制到数组长度，多余部分被丢弃。start是复制元素的起始索引，省略时默认值为0，end是复制元素的结束索引（复制序列不包括结束索引的元素），省略时默认值为arr.length。起始与结束索引均可为负值，代表从数组末尾倒数索引。

这个方法返回修改后的原数组，原数组改变，但不会改变原数组长度。
```javascript
const arr = [1, 2, 3, 4];
arr.copyWithin(1, 2); // 返回arr为[1, 3, 4, 4]
arr.copyWithin(2, 0, 2); // 返回arr为[1, 3, 1, 3]
arr.copyWithin(0, -3, -1); // 返回arr为[3, 1, 1, 3]
arr.copyWithin(-3, -4, -1); // 返回arr为[3, 3, 1, 1]，多余复制序列被丢弃
arr.copyWithin(1); // 返回arr为[3, 3, 3, 1]，多余复制序列被丢弃
```
## 填充
arr.fill(value[, start[, end]]) ES6新方法

该方法用一个值填充数组从起始索引到结束索引（不包括结束索引）的全部元素。value为用来填充元素的值，start是填充起始索引，省略时默认值为0，end是填充结束索引，省略时默认值为arr.length。

该方法返回值为修改后的原数组，原数组改变。该方法可与数组构造器一起使用。
```javascript
const arr = new Array(5).fill(1); // 返回arr为[1, 1, 1, 1, 1]
arr.fill("a"); // 返回arr为["a", "a", "a", "a", "a"]
arr.fill("b", 1); // 返回arr为["a", "b", "b", "b", "b"]
arr.fill("c", 2, 4); // 返回arr为["a", "b", "c", "c", "b"]
arr.fill(5.5, -4, -2); // 返回arr为["a", 5.5, 5.5, "c", "b"] 
```
## 反转
arr.reverse() 该方法会颠倒数组中元素的顺序。

该方法返回值为反转后的原数组，原数组改变。
```javascript
const arr = [1, 2, 3, 4, 5];
arr.reverse(); // 返回arr为[5, 4, 3, 2, 1]
```
## 排序
arr.sort([compareFunction])

排序方法默认按照元素转为字符串的ASCII码升序排列，对数字排序时先将数字转为字符串再比较ASCII码，就会出现10比2小的情况，因为10转为字符串首位是1，1的ASCII码总在2的ASCII码前，这时应使用排序函数排序。
```javascript
const arr = [1, 20, 2, 10];
arr.sort(); // 返回[1, 10, 2, 20]
```
如果指明了compareFunction(a, b)，数组会按照compareFunction(a, b)的返回值进行排序，如果返回值小于0，则a排在b前，如果返回值等于0，则a与b相对位置不变，如果返回值大于0，则a排在b后。

一般比较函数格式如下：
```javascript
function(a, b) {
    if(a < b) {
        return -1;
    }
    if(a = b) {
        return 0;
    }
    return 1;
}
```
如果只是比较数字而非字符串，则可以这样简化函数：
```javascript
function(a, b) {
    return a - b;
}
// 或写成箭头函数
(a, b) => a - b;
```
注意：在谷歌V8引擎和node解释器中，当数组元素个数小于等于10时，sort排序算法使用插入排序，插入排序是稳定排序。而当数组元素个数大于10时，sort排序算法使用快速排序，快速排序是不稳定排序。排序函数使用正值、负值、0三个返回值判定排序时，稳定排序与不稳定排序都不会出错，而排序函数使用布尔值作为返回值判定排序时，稳定排序没有问题，但不稳定排序会出错。因为函数返回值为0与负时都判定为false，不作区分，虽然不稳定排序性能好，但是更容易出错。故使用排序函数时应避免使用布尔值作为返回值，以防止函数元素个数超过10，使用不稳定排序算法出错。
```javascript
const arr = [7, 5, 6, 17, 33, 12, 4, 88, 10, 54, 82]; // 元素超过10个，sort算法用不稳定的快速算法排序
arr.sort((a, b) => a > b);
// Boolean值为排序函数返回值，排序方法返回arr为[ 12, 7, 6, 4, 5, 10, 17, 33, 54, 82, 88 ]
arr.sort((a, b) => a - b);
// 正数、负数、0为排序函数返回值，排序方法返回arr为[ 4, 5, 6, 7, 10, 12, 17, 33, 54, 82, 88 ]
```
排序方法返回排序后的原数组，原数组被改变。
# 数组查找
1. arr.indexOf(element[, start])

    该方法在数组中查找给定的元素element，并返回匹配的第一个元素索引，若不存在则返回-1。start是开始查找的位置索引，默认开始查找位置是0。

2. arr.lastIndexOf(element[, start])

    该方法从后向前查找给定的元素element，并返回匹配的第一个元素索引，若不存在则返回-1。start是开始查找的位置索引，默认开始查找位置是arr.length-1。
```javascript
const o = {name: "Jerry"};
const arr = [1, 5, "a", o, true, 5, [1, 2], "9"];
arr.indexOf(5); // 返回1
arr.lastIndexOf(5); // 返回5
arr.indexOf("a"); // 返回2
arr.lastIndexOf("a"); // 返回2
arr.indexOf({name: "Jerry"}); // 返回-1，对象为引用型，这里的对象与o内容相同，引用自不同内存位置
arr.indexOf(o); // 返回3
arr.indexOf([1, 2]); // 返回-1，数组为引用型，这里的数组与arr中[1, 2]内容相同，但引用自不同内存位置
arr.indexOf("9"); // 返回7
arr.indexOf(9); // 返回-1
arr.indexOf("a", 5); // 返回-1
arr.indexOf(5, 5); // 返回5
arr.lastIndexOf(5, 4); // 返回1
arr.lastIndexOf(true, 3); // 返回-1
```
3. arr.findIndex(function callback (element[, index[, array]]) { }[, thisArg])

    该方法返回数组中符合测试函数的第一个元素的索引，无符合元素返回-1。

4. arr.find(function callback (element[, index[, array]]) { }[, thisArg])

    该方法返回数组中符合测试函数的第一个元素，无符合元素返回undefined。

5. arr.some(function callback (element[, index[, array]]) { }[, thisArg])

    若数组中有一个符合该方法测试函数的元素，则返回true，否则返回false。

6. arr.every(function callback (element[, index[, array]]) { }[, thisArg])

    若数组中所有元素符合该方法测试函数的元素，则返回true，否则返回false。注：空数组也会返回true，因为空数组没有元素，也没有返回任何一个false。

回调函数第一个参数element传入当前元素，第二个元素index传入当前元素的索引，第三个参数array传入调用该方法的数组。thisArg参数可选，可指定this对象的值，省略时，非严格模式默认为window，严格模式默认为undefined。

find与findIndex方法下，数组中从索引0到索引length-1都会执行回调函数，而不仅仅是被赋值的索引，所以在稀疏数组中，那些不存在值的索引也会被调用回调函数，故这两个方法效率低于只遍历有值元素索引的方法。

some与every方法下，只在已经赋值的索引元素上调用回调函数，而未被赋值或被删除的索引的元素上不调用回调函数，故效率高于遍历所有元素索引的方法

第一次调用callback函数时会确定元素的索引范围，因此在这四个方法开始调用后，添加到数组的新元素不会被访问到。被删除的元素仍然会被find和findIndex方法访问到，但不会被some和every方法访问到。如果数组中一个尚未被callback函数访问到的元素被callback函数所改变，那么当callback函数访问到它时，它的值是根据它在数组中的索引访问到的当前值，即改变后的值。

这四个方法本身不改变原数组，除非回调函数中的代码改变了原数组。
```javascript
// 查找数组中被删除的数，若是单数，则删除单数后的第二个数
const arr = [1, 3, 5, 6, 7, 9];
const result = arr.findIndex(function (element, index) {
    if (element === undefined) {
        return true;
    }
    else if (element % 2 !== 0) {
        delete arr[index + 2];
    }
});
console.log(result); // 2 元素虽被删除但仍然能够访问到，访问到的是当前值
console.log(arr); // [1, 3, undefined, undefined, 7, 9]
```
```javascript
// 查找索引大于2，且平方根是整数的元素
const arr = [1, 17, 16, 5, 4, 16, 10, 3, 49];
arr.find((x, i) => i > 2 && Number.isInterger(Math.sqrt(x))); // 返回值为4
```
```javascript
const arr = [5, 7, 12, 15, 17];
arr.some(x => x % 2 === 0); // true; 12是偶数
arr.some(x => Number.isInterger(Math.sqrt(x))); // false; 没有平方数
```
```javascript
const arr = [4, 6, 16, 36];
arr.every(x => x % 2 ===0); // true; 没有奇数
arr.every(x => Number.isInterger(Math.sqrt(x))); // false; 6不是平方数
```
# 数组遍历
## map方法
arr.map(function callback (element[, index[, array]]) { }[, thisArg])

map方法返回一个新数组，新数组中元素是原数组中元素调用回调函数后返回的结果，该方法不改变原数组。

回调函数第一个参数element传入当前元素，第二个元素index传入当前元素的索引，第三个参数array传入调用该方法的数组。thisArg参数可选，可指定this对象的值，省略时，非严格模式默认为window，严格模式默认为undefined。

map方法只在已赋值的元素上调用回调函数，而不在从未赋值或已经被删除的元素上调用回调函数。

map方法中，第一次调用callback函数时就已经确定了元素的索引范围，map方法调用后，新添加到数组的元素不会被访问到；尚未被callback函数访问到的元素被callback函数所改变，被callback函数访问到时，访问到的值为被改变后的当前值；从未被赋值的元素或已经被前面callback函数删除的元素不会被访问到。
```javascript
const kvArray = [
    {key: 1, value: 10},
    {key: 2, value: 20},
    {key: 3, value: 30},
];
const reformattedArray = kvArray.map(function(obj) {
    const rObj = {};
    rObj[obj.key] = obj.value;
    return rObj;
});
// reformattedArray [{1: 10}, {2: 20}, {3: 30}]
// kvArray [{key: 1, value: 10}, {key: 2, value: 20}, {key: 3, value: 30}]
```

## filter方法
arr.filter(function callback (element[, index[, array]]) { }[, thisArg])

filter方法返回一个新数组，筛选出原数组中符合回调函数条件的所有元素放入新数组。该方法不改变原数组。

回调函数第一个参数element传入当前元素，第二个元素index传入当前元素的索引，第三个参数array传入调用该方法的数组。thisArg参数可选，可指定this对象的值，省略时，非严格模式默认为window，严格模式默认为undefined。

filter方法只在已赋值的元素上调用回调函数，而不在从未赋值或已经被删除的元素上调用回调函数。

filter方法中，第一次调用callback函数时就已经确定了元素的索引范围，filter方法调用后，新添加到数组的元素不会被访问到；尚未被callback函数访问到的元素被callback函数所改变，被callback函数访问到时，访问到的值为被改变后的当前值；从未被赋值的元素或已经被前面callback函数删除的元素不会被访问到。
```javascript
const arr = [12, 5, 8, 130, 44];
const filteredArr = arr.filter(element => element >= 10); // [12, 130, 44]
```
```javascript
// 创建一副牌，并按一定的条件筛选牌
// 创建牌
const cards = [];
for (let suit of ["H", "C", "D", "S"]) {
    for (let value = 1; value <= 13; value++) {
        cards.push({suit, value});
    }
}
console.log(cards); // [{suit: "H", value: 1}, {suit: "H", value: 2}, ...{suit: "S", value: 13}]
const suits = {"H": "\u2665", "C": "\u2663", "D": "\u2666", "S": "\u2660"}; // 花色图案字符串
const values = {1: "A", 11: "J", 12: "Q", 13: "K"}; // 牌面大小字符串
for(let i = 2; i < 11; i++) {
    values[i] = i.toString();
}
console.log(values); // {1: "A", 2: "2", 3: "3", ...13: "K"}
function stringCards(card) { // 将牌输出成图案字符串
    return suits[card.suit] + values[card.value];
}
const cardsFilter1 = cards.filter(card => card.value === 2).map(stringCards); // 筛选值为2的牌，
console.log(cardsFilter1); // ["♥2", "♣2", "♦2", "♠2"]
const cardsFilter2 = cards.filter(card => card.suit !== "D" && card.value <= 4).map(stringCards); // 筛选不是方块且值小于等于4的牌
console.log(cardsFilter2); // ["♥A", "♥2", "♥3", "♥4", "♣A", "♣2", "♣3", "♣4", …]
```

## reduce方法
arr.reduce(function callback (accumulator, element[, index[, array]]) { }[, initialAccumulator])

reduce方法对累加器和数组中的每个元素（从左至右）执行回调函数，将其减少为单个值。

回调函数第一个参数accumulator是累加器累加后的返回值，它是上一次调用回调时返回的累加值，或累加器的初始值initialAccumulator。element是当前传入的元素值，index是当前传入元素的索引，array是调用reduce方法的数组本身。initialAccumulator是第一次调用回调函数的accumulator的初始值，若没有提供initialAccumulator的值，则使用数组第一个元素值。在没有初始值的空数组上调用reduce方法会报错。

reduce方法的返回值是回调函数累计处理后的返回值。

reduce方法依次为数组中每个元素执行回调函数，不包括数组中从未被赋值的元素和已经被删除的元素。回调函数第一次执行时accumulator与element有两种情况：1、调用reduce时提供了initialAccumulator，accumulator取值为initialAccumulator，element取值为数组的第一个元素；2、调用reduce时未提供initialAccumulator，accumulator取值为数组中第一个元素，element取值为数组的第二个元素。即未提供initialAccumulator，回调函数从索引为1的位置开始执行，提供initialAccumulator时，回调函数从索引0开始执行。

若数组为空且未提供initialAccumulator，则会抛出TypeError。若数组只有一个元素且未提供initialAccumulator，或数组为空但提供了initialAccumulator，那么唯一的值将被返回，回调函数并不会执行。

reduce最常见用法，数组元素求和
```javascript
const arr = [1, 4, 7, 9, 3, 5, 8, 10];
const sum = arr.reduce(function(sum, num) {
    return sum += num;
});
console.log(sum); // 47
// ES6 写法
const sum = arr.reduce((sum, num) => sum += num);
// 也可将返回值写为sum + num，结果与sum += num一样
```

二维数组转化为一维
```javascript
const flattened = [[0, 1], [2, 3], [4, 5]].reduce((arr, element) => arr.concat(element), []);
console.log(flattened); // [0, 1, 2, 3, 4, 5]
// 也可以用ES6解构赋值法
const flattened = [[0, 1], [2, 3], [4, 5]].reduce((arr, element) => [...arr, ...element], []);
```

计算数组中每个元素出现的次数
```javascript
const names = ["Alice", "Bob", "Tiff", "Bob", "Bruce", "Alice"];
const countedNames = names.reduce(function(counted, name) {
    if(!counted[name]) {
        counted[name] = 1;
    }
    else counted[name] += 1;
    return counted;
}, {});
console.log(countedNames); // {Alice: 2, Bob: 2, Tiff: 1, Bruce: 1}
```

数组去重
```javascript
const arr = [1,2,1,2,3,5,4,5,3,4,4,4,4];
const result = arr.sort().reduce((resultArr, num) => {
    // 先用sort排序，使reduce回调函数简化。arr无大于10的数，可直接sort默认排序，当有大于10的数时应使用排序函数
    if(resultArr.length === 0 || resultArr[resultArr.length - 1] !== num) {
        resultArr.push(num);
    }
    return resultArr;
}, []);
console.log(result); // [1, 2, 3, 4, 5]
```
# 字符串连接
arr.join([separator])

join方法将一个数组的所有元素都转化成字符串，并用一个连接符将这些元素字符串连接成一个字符串，并返回这个字符串。

separator参数用来指定字符串中每个元素的分隔符，省略时，separator参数默认值为","，若用""空字符串为分隔符，则每个元素间没任何字符。

返回值是一个数组元素连接的字符串，若数组为空则返回空字符串，若是已删除元素或未定义元素undefined或null，则该元素为一个空字符串。该方法不改变原数组。
```javascript
const arr = [1, null, "hello", "world", true, undefined];
delete arr[3];
arr.join(); // "1,,hello,,true," 
arr.join(""); // "1hellotrue"
arr.join(" -- "); // "1 --  -- hello --  -- true -- "
```

# 总结

|     方法     |         用法         |      返回值     |  是否修改当前数组  |
|     ----     |  -----------------  |  -------------  | ----------------- |
|  push        |  添加元素到数组末尾   |  新数组长度      |   修改当前数组   |
|  pop         |  删除数组末尾的元素   |  被删除元素      |   修改当前数组   |
|  shift       |  删除数组开头的元素   |  新数组长度      |   修改当前数组   |
|  unshift     |  添加元素到数组开头   |  被删除元素      |   修改当前数组   |
|  concat      |  串接数组到原数组末尾 |  新的数组        |   不修改        |
|  slice       |  获取子数组          |  新的数组        |   不修改        |
|  splice      |  删除添加元素        |  被删除元素的数组 |   修改当前数组   |
|  copyWithIn  |  复制自身元素并替换   |  修改后的原数组  |   修改当前数组   |
|  fill        |  用值填充数组        |  修改后的原数组   |   修改当前数组  |
|  reverse     |  颠倒数组元素顺序     |  修改后的原数组  |   修改当前数组   |
|  sort        |  对数组元素排序      |  修改后的原数组   |   修改当前数组   |
|  indexOf     |  查找数组索引        |  首个符合元素索引 |   不修改        |
|  lastIndexOf |  从后向前查找数组索引 |  首个符合元素索引 |   不修改        |
|  findIndex   |  查找数组索引（函数） |  首个符合元素索引 |   不修改        |
|  find        |  查找数组元素        |  首个符合元素     |   不修改        |
|  some        |  判断是否有符合元素   |  布尔值          |   不修改        |
|  every       |  判断是否全部元素符合 |  布尔值          |   不修改        |
|  map         |  使用函数遍历元素     |  新数组          |   不修改       |
|  filter      |  使用函数筛选元素     |  选出元素的新数组 |   不修改       |
|  reduce      |  将数组减少为一个值   |  回调函数返回值   |   不修改       |
|  join        |  将元素转为字符串合并 |  字符串          |   不修改        |