---
title: javascript如何获取伪元素的css属性
date: 2019-03-08 20:32:45
tags: 伪元素
categories: javascript
---

# 如何获取伪元素的CSS属性

伪元素是例如::after，::before等形式的css生成的元素。javascript中没有直接获取伪元素的选择器，但是有获取伪元素属性的方法。

window.getComputedStyle()

我们可以使用window.getComputedStyle()方法获取伪元素，再使用getPropertyValue()方法获取属性值。

语法：window.getComputedStyle(element[, pseudoElement])

element为伪元素所在的元素，pseudoElement为伪元素自身。

```javascript
// 假如我们要获取的是class为test的::after伪元素，并获取到它的content值
const element = document.querySelector(".test");
const afterStyle = window.getComputedStyle(element, "::after")
const afterContent = afterStyle.getPropertyValue("content");
```

如果想要修改伪元素的属性怎么办呢？可以直接在取到的属性上赋值吗？答案是否定的，因为这里的style是计算出来的（看方法名就知道），所以是只读的，无法修改。我们并没有办法直接修改到伪元素的属性，只能通过获取styleSheet，再使用addRules（兼容IE）和insertRules（现代浏览器）方法来新增覆盖掉原来的伪元素样式。或者利用DOM原素的data-属性来修改。或者通过改变class类来改变伪元素。

```javascript
// 修改styleSheet
document.styleSheets[0].addRule('.test::after','content: hellow'); // 支持IE
document.styleSheets[0].insertRule('.test::after { content: hellow }', 0); // 支持非IE的现代浏览器
```

```html
<!-- CSS代码 -->
<style>
.test::after {
  content: attr(data-attr);
}
</style>

<!-- HTML代码 -->
<div class="test" data-attr="hellow"></div>

<!-- JacaScript代码 -->
<script>
const element = document.querySelector(".test");
element.setAttribute("data-attr", "hellow");
</script>
```