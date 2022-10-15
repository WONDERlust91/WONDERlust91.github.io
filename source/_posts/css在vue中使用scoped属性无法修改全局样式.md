---
title: css在vue组件中使用scoped属性无法修改另一个组件的全局样式
date: 2019-06-01 10:10:15
tags: scoped css
categories: css
---

我们在 vue 的使用过程中常常会遇到这样的问题，想要在局部的 vue 文件中修改一个组件中的全局样式，且不影响全局，只在当前的 vue 文件中生效。我们直觉上就是通过 style 标签上 scoped 属性来实现局部生效，不影响全局。但是事实上这样做并没有效果，局部的样式并没有生效，这是为什么呢？

让我们用一个例子来说明问题，假设我们在当前组件 A 中使用的另一个组件 B，组件 B 中使用了一个全局的样式：字为红色。我们想让 B 组件在 A 组件中使用时字为蓝色，而在其他地方使用时字依然是红色。我们在 A 组件中给 style 标签设置了 scoped 属性，然后设置.foo 的类为蓝色，结果并未生效。

<!-- more -->

```html
<!-- component B -->
<template>
  <div class="B">
    <div class="foo">这里字为红色</div>
  </div>
</template>
<script>
  export default {
    name: "B"
  };
</script>
<style>
  .foo {
    color: red;
  }
</style>
```

```html
<!-- component A -->
<template>
  <div class="A">
    <b></b>
    <!-- 我们想让B中的字只在A中是蓝色，发现并没有效果 -->
  </div>
</template>
<script>
  import B from "@/component/B.vue";
  export default {
    name: "A",
    components: {
      B
    }
  };
</script>
<style scoped>
  .foo {
    color: blue;
  }
</style>
```

打开开发者工具，我们发现，使用了 scoped 属性后，A 组件中所有的 html 标签都会加上一个类似 data-v-039c5b43 的属性，A 中引用的 B 组件根 div 标签也加上了这样一个 data-v 的属性，但是 B 组件内部的标签并没有被加上。也就是说，scoped 属性只对当前页面直接使用的标签生效。因此，我们想要修改的.foo 类应该是" .B[data-v-039c5b43] .foo "，而使用了 scoped 属性我们在 A 组件 css 中修改的类却是 " .foo[data-v-039c5b43] "，这并不是我们想要修改的那个类，所以把字体变为蓝色并没有像我们想象中那样生效。

解决方案：

1.删除 A 组件 style 标签的 scoped 属性，就可以修改到全局的类

```html
<!-- 去除scoped属性 -->
<style>
  .foo {
    color: blue;
  }
</style>
```

2.保留 style 标签的 scoped 属性，重新引入一个全局的 css 样式文件来修改 B 组件的全局样式。（不可以在 style 标签中以@import 形式引入，这样还是受到 scoped 属性影响，应该在 script 标签中直接 import）

```css
/* global.css */
.A .foo {
  color: blue;
}
```

```html
<!-- component A -->
<template>
  <div class="A">
    <b></b>
    <!-- 我们想让B中的字只在A中是蓝色，发现并没有效果 -->
  </div>
</template>
<script>
  import "global.css";
  import B from "@/component/B.vue";
  export default {
    name: "A",
    components: {
      B
    }
  };
</script>
<style scoped></style>
```

结论：对全局 css 样式的修改应该在全局中实现，若只要在局部修改全局 css 样式，不应该通过 scoped 属性实现，而应该在全局中用多级 css 类来限制作用范围。

参考链接[https://www.jianshu.com/p/f9a8b7784655](https://www.jianshu.com/p/f9a8b7784655);
