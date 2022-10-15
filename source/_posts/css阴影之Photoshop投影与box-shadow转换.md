---
title: css阴影之Photoshop投影与box-shadow转换
date: 2020-06-13 13:59:06
tags:
- Photoshop投影
- box-shadow
categories: css
---

## box-shadow

box-shadow属性用于在元素上添加阴影效果。同一个元素上可以设置多个阴影效果，使用逗号将它们分开即可。该属性可设置的值包括x轴偏移量（x-offset），y轴偏移量（y-offset），模糊半径（blur-radius），扩散半径（spread-radius），颜色（color）。

若元素设置了border-radius属性，阴影同样会有圆角效果。多个阴影在z轴上的顺序和text-shadows规则相同，即第一个阴影在最上面。

### box-shadow属性值设置规则

- 二至四个值时
  
  - 二个值时，被视为offset-x与offset-y

  - 三个值时，第三个值为blur-radius

  - 四个值时，第四个值为spread-radius

- inset 可选值，表示阴影向内

- color 可选值，指代阴影颜色

### 取值

inset 未指定inset时阴影默认向外扩散，使用inset关键字使阴影落在盒子内部，看起来像是内容被压低了，此时阴影在边框之内，背景之上，内容之下。

offset 两个offset为length类型值。用来设置阴影偏移量。offset-x 设置阴影水平偏移，正值阴影位于元素右边，负值阴影位于元素左边。offset-y 设置阴影垂直偏移，正值阴影位于元素下方，负值阴影位于元素上方。

blur-radius 必需为正值，设置阴影模糊面积，越大阴影颜色越淡。默认为0，阴影边缘锐利。

spread-radius 默认为0，阴影与元素同样大小。取正值时，阴影扩大，取负值时，阴影收缩。需要考虑inset属性的影响。

color 未指定时由浏览器决定，通常为color的值，safari中为透明。

## Photoshop投影与css阴影的换算

结构：

- 混合模式：Photoshop提供了各种混合模式，css仅支持正常模式

- 不透明度：相当于box-shadow中color的rgba中的a值

- 角度：投影的角度

- 距离：阴影的距离，根据角度和距离可以换算出css3中的offset-x与offset-y

  - offset-x：距离 * cos(180度 - 角度)

  - offset-y：距离 * sin(180度 - 角度)

- 扩散：阴影的扩散，根据扩散与大小可以计算出spread-radius与blur-radius

- 大小：阴影的大小

  - spread-radius：扩散 * 大小

  - blur-radius: 大小 - 扩散 * 大小

text-shadow因为没有spread-radius所以不能完全实现Photoshop中的效果。
