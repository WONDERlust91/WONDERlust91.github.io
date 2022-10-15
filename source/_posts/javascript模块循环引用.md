---
title: javascript模块循环引用
date: 2022-02-11 19:23:53
tags:
  - 循环引用
  - 循环依赖
  - circular dependency
categories: javascript
---

循环引用，也称为循环依赖（circular dependency），通常指 a 脚本的执行依赖了 b 脚本，而 b 脚本的执行又依赖了 a 脚本。

上面概念中的举例，可以说是最简单的情况，我们很容易就能避免，但实际的项目中，依赖关系复杂了以后，很难避免。在我们不注意的情况下，就会出现 a 依赖 b，b 依赖 c，c 又依赖了 a 这样的情况。那么考虑到这样的情况不可避免，javascript 的引擎针对 CommonJS 模块与 ECMAScript 模块分别制定了不同的模块加载机制。

虽然 javascript 引擎支持了循环加载，但我们在书写代码时，还是应该尽量避免循环引用。循环引用会导致不可预期的错误，例如递归加载，使程序无法执行，或发生改变引用顺序就导致程序报错的情况，例如之前一篇文章{% post_link react在组件外使用redux的dispatch %}，就是循环引用导致的问题。

接下来我们就分别介绍一下 CommonJS 与 ECMAScript 的循环加载机制

<!-- more -->

## CommonJS 循环引用

根据[NodeJS 官网给出的循环引用示例](https://nodejs.org/api/modules.html#cycles)，当发生循环引用时，模块的返回值可能是尚未执行完成的结果。

考虑如下情况：

```js
// a.js
console.log("a starting");
exports.done = false;
const b = require("./b.js");
console.log("in a, b.done = %j", b.done);
exports.done = true;
console.log("a done");
```

```js
// b.js
console.log("b starting");
exports.done = false;
const a = require("./a.js");
console.log("in b, a.done = %j", a.done);
exports.done = true;
console.log("b done");
```

```js
// main.js
console.log("main starting");
const a = require("./a.js");
const b = require("./b.js");
console.log("in main, a.done = %j, b.done = %j", a.done, b.done);
```

当 main.js 加载 a.js 时，a.js 又需要加载 b.js。b.js 加载时又试图加载 a.js。为了防止无限循环加载，一份尚未完成的 a.js 的导出对象被返回给了 b.js，当 b.js 加载完成后，将 b.js 的导出对象提供给 a.js 作为模块。

在 main.js 中加载了两个模块，加载过程的输出记录为：

```text
$ node main.js

main starting
a starting
b starting
in b, a.done = false
b done
in a, b.done = true
a done
in main, a.done = true, b.done = true
```

可见：

- 在 b.js 中 a.js 并没有执行完成；

- 在 main 中，b.js 并没有第二次加载，使用了 a.js 中加载 b.js 的缓存结果；

结论：CommonJS 处理循环引用，依靠暂停未完成的模块执行来实现，对于重复引入的模块，则是直接使用缓存。

这里还要提醒一次，一定要谨慎对待循环加载机制，尽量不要使用。

## ECMAScript 循环引用

ECMAScript 模块的运行机制与 CommonJS 截然不同，网上有很多资料认为 ECMAScript 模块支持循环引用，其实是不准确的。

ECMAScript Module 的实现是通过分析文件引用关系，从最外层依赖引用，一直分析到最内层，然后从最内层依赖向外执行，执行时 import 关键字先声明依赖的引用，类似变量提升机制，后续执行到引入模块的 export 关键字进行赋值（这里仅是为了好理解，类比为变量提升、赋值、临时死区等，实际的实现是比较复杂的对象 getter 与 setter 等）。

此时如果最内层循环引用了外层，且进行了同步式的调用外层，那么内层本就是由外层跳转进入的，此时外层的 export 关键字尚未执行，内层引入的外层，还未进行赋值，类似变量的临时死区。那么 javascript 引擎会向你报错，外层依赖尚未初始化（node 中报错为 `Cannot access 'XXX' before initialization`，浏览器中则会报类似 `XXX is undefined` 错误），即不支持循环引用。

示例使用 mjs 后缀，因为 node 环境运行，不配置 package.json 时，使用 js 后缀文件会报错：`SyntaxError: Cannot use import statement outside a module`。

```js
// a.mjs
console.log("a starting");
import { b } from "./b.mjs";
export const a = { done: false };
console.log("in a, b.done = %j", b.done);
a.done = true;
console.log("a done");
```

```js
// b.mjs
console.log("b starting");
import { a } from "./a.mjs";
export const b = { done: false };
console.log("in b, a.done = %j", a.done);
b.done = true;
console.log("b done");
```

```js
// main.mjs
console.log("main starting");
import { a } from "./a.mjs";
import { b } from "./b.mjs";
console.log("in main, a.done = %j, b.done = %j", a.done, b.done);
```

运行结果

```text
$ node main.mjs

b starting
file://path-to-file/b.mjs:4
  console.log("in b, a.done = %j", a.done);
ReferenceError: Cannot access 'a' before initialization
```

另一种情况是在最内层并未进行同步调用，而是导出了使用循环依赖的函数，或对循环的依赖进行了异步调用，那么这种情况就会相对复杂，是否会发生错误，取决于函数或异步调用的时机，若这个时机在初始化之前，则会报错，且在真实复杂依赖的项目中，很难排查。若调用时机，在初始化之后，则不会报错，造成了一种 ECMAScript 支持循环依赖的错觉。

```js
// a.mjs
console.log("a starting");
import { b } from "./b.mjs";
export const a = { done: false };
console.log("in a, b.done = %j", b.done);
a.done = true;
console.log("a done");
```

```js
// b.mjs
console.log("b starting");
import { a } from "./a.mjs";
export const b = { done: false };
setTimeout(() => {
  console.log("in b, a.done = %j", a.done);
  b.done = true;
}, 500);
console.log("b done");
```

```js
// main.mjs
console.log("main starting");
import { a } from "./a.mjs";
import { b } from "./b.mjs";
console.log("in main, a.done = %j, b.done = %j", a.done, b.done);
```

运行结果

```text
$ node main.mjs

b starting
b done
a starting
in a, b.done = false
a done
main starting
in main, a.done = true, b.done = false
in b, a.done = true
```

- 从内向外（b-a-main），先执行同步代码；

- 执行 b 时虽然 a 循环引用，但未同步调用，相当于仅是在 b 中声明了 a；

- b 执行完成后，a 中的 b 相当于已声明，故向外执行到 a 时，同步执行没有问题；

- 再向外执行 main，a、b 都已声明，a.done 在 a 执行时就已设置为 true，而 b.done 异步尚未执行，仍为 false；

- 最后执行异步的 b，此时同步 a 已执行完成，b 虽然循环引用了 a，但此时 a 已有值，并不会报错，造成了 ECMAScript 支持循环引用的错觉。

结论：ECMASCript Module 的机制实质上并不是网上所说的支持循环引用，相反会对循环依赖进行报错提示。但由于它的实现机制，导致 ES Module 并不会严格检查到异步调用到的循环依赖，而调用时机的不同，导致了不是一定会发生错误。这也就是大家常说的，循环引用依赖会导致不可预知的问题。

因此循环引用是一个坏的实践，应该尽量避免。

webpack 插件 circular-dependency-plugin 可以帮助你检测项目中存在的所有循环依赖，尽早发现潜在的循环依赖会免去未来很大的麻烦。

> 参考资料
>
> [JavaScript 模块的循环加载](https://www.ruanyifeng.com/blog/2015/11/circular-dependency.html)
>
> [NodeJS API Modules Cycles](https://nodejs.org/api/modules.html#cycles)
>
> [深入分析 JavaScript 模块循环引用](https://jishuin.proginn.com/p/763bfbd62ab5)
>
> [探索 JavaScript 中的依赖管理及循环依赖](https://zhuanlan.zhihu.com/p/33049803)
>
> [循环依赖](https://github.com/CommanderXL/Biu-blog/issues/49)
>
> [ES6 中循环引用的坑](http://yangguang1029.github.io/2017/11/13/es6-circle-import/)
>
> [Node.js 中的循环依赖（源码解析）](https://segmentfault.com/a/1190000004151411)
