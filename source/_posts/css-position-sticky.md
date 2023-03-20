---
title: css position sticky
date: 2023-03-18 15:20:47
tags:
  - sticky
  - position
categories: css
---

## Introduction

The element is positioned according to the normal flow of the document, and then offset relative to its nearest scrolling ancestor and containing block (nearest block-level ancestor), including table-related elements, based on the values of top, right, bottom, and left. The offset does not affect the position of any other elements.

This value always creates a new stacking context. Note that a sticky element "sticks" to its nearest ancestor that has a "scrolling mechanism" (created when overflow is hidden, scroll, auto, or overlay), even if that ancestor isn't the nearest actually scrolling ancestor.

<!-- more -->

`position: sticky` can be thought of as a combination of `position: relative` and `position: fixed`. When the element is within the screen area, it behaves like `relative`. However, as the element begins to scroll out of view, it behaves as if it were `fixed`.

```css
.sticky-box {
  position: sticky;
  top: 0;
}
```

It's treated as relatively positioned until its containing block crosses a specified threshold (such as setting top to value other than auto) within its flow root (or the container it scrolls within), at which point it is treated as "stuck" until meeting the opposite edge of its containing block.

## Deep dive into Sticky

1. The sticky element will only stick to the nearest ancestor element that has a "scrolling mechanism", which is created by setting `overflow` to `hidden`, `scroll`, `auto`, or `overlay`. To make the sticky element stick to the root element, you need to make sure that all ancestor elements between the sticky element and the root element have `overflow: visible`.

   ```html
   <style>
     .root {
       width: 350px;
       height: 200px;
       background: rgba(255, 255, 255, 0.1);
       overflow: auto;
     }
     .parent {
       width: 300px;
       height: 300px;
       background: blue;
       margin: 20px auto 0 auto;
     }
     .sticky {
       position: sticky;
       top: 0;
       background: green;
       height: 20px;
     }
   </style>
   <!-- scrolling mechanism happens on root -->
   <div class="root">
     <div class="parent">
       <div class="sticky"></div>
     </div>
   </div>
   ```

    <div style="
      width: 350px;
    height: 200px;
    background: rgba(255,255,255,0.1);
    overflow: auto;
    ">
    <div style="
      width: 300px;
      height: 300px;
      background: blue;
      margin: 20px auto 0 auto;
    ">
      <div style="
        position: sticky;
        top: 0;
        background: green;
        height: 20px;
      "></div>
    </div>
    </div>

    <br />

   ```html
   <style>
     .root {
       width: 350px;
       height: 200px;
       background: rgba(255, 255, 255, 0.1);
       overflow: auto;
     }
     .parent {
       width: 300px;
       height: 300px;
       background: blue;
       margin: 20px auto 0 auto;
       /* shouldn't have any scrolling mechnism between scrollable element (root) and sticky element */
       overflow: auto;
     }
     .sticky {
       position: sticky;
       top: 0;
       background: green;
       height: 20px;
     }
   </style>
   <div class="root">
     <!-- scrolling mechanism happens on parent, but parent element is not scrollable -->
     <div class="parent">
       <div class="sticky"></div>
     </div>
   </div>
   ```

    <div style="
      width: 350px;
      height: 200px;
      background: rgba(255,255,255,0.1);
      overflow: auto;
    ">
      <div style="
        width: 300px;
        height: 300px;
        background: blue;
        margin: 20px auto 0 auto;
        overflow: auto;
      ">
        <div style="
          position: sticky;
          top: 0;
          background: green;
          height: 20px;
        "></div>
      </div>
    </div>

    <br />

2. When the parent element has the same height as the sticky element, there is no need for the sticky behavior since the sticky element will not move out of the parent element. Therefore, the sticky behavior will not take effect in this case.

   ```html
   <style>
     .root {
       width: 350px;
       height: 200px;
       background: rgba(255, 255, 255, 0.1);
       overflow: auto;
     }
     .parent {
       width: 300px;
       height: 20px;
       background: blue;
       margin: 20px auto 0 auto;
     }
     .sticky {
       position: sticky;
       top: 0;
       background: green;
       height: 20px;
     }
     .others {
       width: 300px;
       height: 300px;
       background: orange;
     }
   </style>
   <div class="root">
     <!-- sticky element can not fixed outside the parent -->
     <div class="parent">
       <div class="sticky"></div>
     </div>
     <div class="others"></div>
   </div>
   ```

    <div style="
      width: 350px;
      height: 200px;
      background: rgba(255,255,255,0.1);
      overflow: auto;
    ">
      <div style="
        width: 300px;
        height: 20px;
        background: blue;
        margin: 20px auto;
      ">
        <div style="
          position: sticky;
          top: 0;
          background: green;
          height: 20px;
        "></div>
      </div>
      <div style="
        width: 300px;
        height: 300px;
        background: orange;
        margin: auto;
      "></div>
    </div>

    <br />

3. When there are multiple sticky elements with the same position in a parent element, they will stack on top of each other in the order they appear in the HTML markup. The first sticky element will stick to its nearest ancestor with a scrolling mechanism as usual, and will be at the bottom of the stack, and subsequent sticky elements will be layered on top of it when scrolled to the threshold.

   ```html
   <style>
     .root {
       width: 350px;
       height: 200px;
       background: rgba(255, 255, 255, 0.1);
       overflow: auto;
     }
     .parent {
       width: 300px;
       height: 300px;
       margin: 20px auto 0 auto;
     }
     .sticky-1 {
       position: sticky;
       top: 0;
       background: green;
       height: 30px;
     }
     .sticky-2 {
       position: sticky;
       top: 0;
       background: orange;
       height: 20px;
       margin-top: 20px;
     }
     .sticky-3 {
       position: sticky;
       top: 0;
       background: pink;
       height: 10px;
       margin-top: 20px;
     }
   </style>
   <div class="root">
     <div class="parent">
       <div class="sticky-1"></div>
       <div class="sticky-2"></div>
       <div class="sticky-3"></div>
     </div>
   </div>
   ```

    <div style="
      width: 350px;
      height: 200px;
      background: rgba(255,255,255,0.1);
      overflow: auto;
    ">
      <div style="
        width: 300px;
        height: 300px;
        background: rgba(255, 255, 255, 0.1);
        margin: 20px auto;
      ">
        <div style="
          position: sticky;
          top: 0;
          background: green;
          height: 30px;
        "></div>
        <div style="
          position: sticky;
          top: 0;
          background: orange;
          height: 20px;
          margin-top: 20px;
        "></div>
        <div style="
          position: sticky;
          top: 0;
          background: pink;
          height: 10px;
          margin-top: 20px;
        "></div>
      </div>
    </div>

    <br />

4. When there are multiple parent elements each with a sticky element that has the same position defined, and the scrolling mechanism only happens on the root element, the topmost sticky element will stick until the second sticky element reaches its threshold, at which point the second sticky element will take over as the new topmost sticky element. This process continues for any subsequent sticky elements.

   ```html
   <style>
     .root {
       width: 350px;
       height: 200px;
       background: rgba(255, 255, 255, 0.1);
       overflow: auto;
     }
     .parent-1 {
       width: 300px;
       height: 300px;
       background: blue;
       margin: 20px auto 0 auto;
     }
     .sticky-1 {
       position: sticky;
       top: 0;
       background: green;
       height: 20px;
     }
     .parent-2 {
       width: 300px;
       height: 300px;
       background: red;
       margin: 0 auto 20px auto;
     }
     .sticky-2 {
       position: sticky;
       top: 0;
       background: orange;
       height: 20px;
     }
   </style>
   <div class="root">
     <div class="parent-1">
       <div class="sticky-1"></div>
     </div>
     <div class="parent-2">
       <div class="sticky-2"></div>
     </div>
   </div>
   ```

    <div style="
      width: 350px;
      height: 200px;
      background: rgba(255,255,255,0.1);
      overflow: auto;
    ">
      <div style="
        width: 300px;
        height: 300px;
        background: blue;
        margin: 20px auto 0 auto;
      ">
        <div style="
          position: sticky;
          top: 0;
          background: green;
          height: 20px;
        "></div>
      </div>
      <div style="
        width: 300px;
        height: 300px;
        background: red;
        margin: 0 auto 20px auto;
      ">
        <div style="
          position: sticky;
          top: 0;
          background: orange;
          height: 20px;
        "></div>
      </div>
    </div>

    <br />

- Conclusion: The sticky positioning behavior in CSS allows an element to be positioned relative to its closest scrolling ancestor, which is the nearest ancestor element with a scrolling mechanism created by setting the overflow property to anything other than visible. When an element with a position: sticky property is scrolled beyond the top edge of its closest scrolling ancestor, it will remain in view until it reaches the bottom edge of its parent element or a specified offset threshold. If there are multiple sticky elements within the same parent element with the same position value, they will stack on top of each other in the order they appear in the HTML markup.

## Prevant anchor links from scrolling behind a sticky element

A sticky or fixed menu is a popular UX solution that displays a navbar at the top of the page, providing access to menu items at all times, even while scrolling through pages.

However, when a page uses anchors in the menu to allow users to jump to specific sections of the page, it can cause an issue. When an anchor is clicked, the page will scroll to position the anchor at the top of the viewport, which may obscure the section title and part of the content behind the fixed menu.

<div style="
  width: 375px;
  height: 200px;
  overflow: auto;
  background: rgba(255, 255, 255, 0.1);
">
  <div style="
    width: 325px;
    height: 40px;
    position: sticky;
    top: -1px;
    background: rgba(0, 0, 255, 0.8);
    margin: 20px auto 0 auto;
  ">
    <a style="margin: 0 4px;" href="#sticky-demo-1">Scroll to demo-1</a>
    <a style="margin: 0 4px;" href="#sticky-demo-2">Scroll to demo-2</a>
  </div>
  <div id="sticky-demo-1" style="
    width: 325px;
    height: 40px;
    background: red;
    margin: auto;
    color: black;
  ">demo-1</div>
  <div id="sticky-demo-2" style="
    width: 325px;
    height: 300px;
    background: yellow;
    margin: 0 auto 20px auto;
    color: black;
  ">demo-2</div>
</div>

<br />

To solve this issue, a common solution has been to add top margin and padding to the anchor sections, but this can lead to a lack of control over the page layout. Fortunately, we now have a simple one-line CSS solution: `scroll-margin-top` and `scroll-padding-top`.

The `scroll-padding-top` property is applied to the parent container and acts like a CSS top padding, defining offsets from the top of the scrolling area. You can use various values such as `px`, `em`, `rem`, `vh`, `%`, or `auto`.

```html
<style>
  .root {
    width: 375px;
    height: 200px;
    overflow: auto;
    background: rgba(255, 255, 255, 0.1);
    scroll-padding-top: 40px;
  }
  .navbar {
    width: 325px;
    height: 40px;
    position: sticky;
    top: 0;
    background: rgba(0, 0, 255, 0.8);
    margin: 20px auto 0 auto;
  }
  .navbar a {
    margin: 0 4px;
  }
  .demo-1 {
    width: 325px;
    height: 40px;
    background: red;
    margin: auto;
    color: black;
  }
  .demo-2 {
    width: 325px;
    height: 300px;
    background: yellow;
    margin: 0 auto 20px auto;
    color: black;
  }
</style>
<div class="root">
  <div class="navbar">
    <a href="#demo-1">Scroll to demo-1</a>
    <a href="#demo-2">Scroll to demo-2</a>
  </div>
  <div id="demo-1" class="demo-1"></div>
  <div id="demo-2" class="demo-2"></div>
</div>
```

<div style="
  width: 375px;
  height: 200px;
  overflow: auto;
  background: rgba(255,255,255,0.1);
  scroll-padding-top: 40px;
">
  <div style="
    width: 325px;
    height: 40px;
    position: sticky;
    top: -1px;
    background: rgba(0, 0, 255, 0.8);
    margin: 20px auto 0 auto;
  ">
    <a style="margin: 0 4px;" href="#sticky-demo-1-p">Scroll to demo-1</a>
    <a style="margin: 0 4px;" href="#sticky-demo-2-p">Scroll to demo-2</a>
  </div>
  <div id="sticky-demo-1-p" style="
    width: 325px;
    height: 40px;
    background: red;
    margin: auto;
    color: black;
  ">demo-1</div>
  <div id="sticky-demo-2-p" style="
    width: 325px;
    height: 300px;
    background: yellow;
    margin: 0 auto 20px auto;
    color: black;
  ">demo-2</div>
</div>

<br />

The `scroll-margin-top` property defines the top margin of the anchor sections, that the browser uses when snapping the scrolled element into place. This property uses length units such as `px`, `em`, `rem`, `vh`, and so on.

```html
<style>
  .root {
    width: 375px;
    height: 200px;
    overflow: auto;
    background: rgba(255, 255, 255, 0.1);
  }
  .navbar {
    width: 325px;
    height: 40px;
    position: sticky;
    top: 0;
    background: rgba(0, 0, 255, 0.8);
    margin: 20px auto 0 auto;
  }
  .navbar a {
    margin: 0 4px;
  }
  .demo-1 {
    width: 325px;
    height: 40px;
    background: red;
    margin: auto;
    scroll-margin-top: 40px;
    color: black;
  }
  .demo-2 {
    width: 325px;
    height: 300px;
    background: yellow;
    margin: 0 auto 20px auto;
    scroll-margin-top: 40px;
    color: black;
  }
</style>
<div class="root">
  <div class="navbar">
    <a href="#demo-1">Scroll to demo-1</a>
    <a href="#demo-2">Scroll to demo-2</a>
  </div>
  <div id="demo-1" class="demo-1"></div>
  <div id="demo-2" class="demo-2"></div>
</div>
```

<div style="
  width: 375px;
  height: 200px;
  overflow: auto;
  background: rgba(255,255,255,0.1);
">
  <div style="
    width: 325px;
    height: 40px;
    position: sticky;
    top: -1px;
    background: rgba(0, 0, 255, 0.8);
    margin: 20px auto 0 auto;
  ">
    <a style="margin: 0 4px;" href="#sticky-demo-1-m">Scroll to demo-1</a>
    <a style="margin: 0 4px;" href="#sticky-demo-2-m">Scroll to demo-2</a>
  </div>
  <div id="sticky-demo-1-m" style="
    width: 325px;
    height: 40px;
    background: red;
    margin: auto;
    scroll-margin-top: 40px;
    color: black;
  ">demo-1</div>
  <div id="sticky-demo-2-m" style="
    width: 325px;
    height: 300px;
    background: yellow;
    margin: 0 auto 20px auto;
    scroll-margin-top: 40px;
    color: black;
  ">demo-2</div>
</div>

<br />

> It's important to remember that these properties apply only to scroll-snapping and do not affect the actual padding of the HTML element or the defined margin between anchor sections.

> References
>
> [张鑫旭 - 杀了个回马枪，还是说说 position:sticky 吧](https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/)
>
> [张鑫旭 - 深入理解 position sticky 粘性定位的计算规则](https://www.zhangxinxu.com/wordpress/2020/03/position-sticky-rules/)
>
> [One line CSS solution to prevent anchor links from scrolling behind a sticky or fixed header](https://getpublii.com/blog/one-line-css-solution-to-prevent-anchor-links-from-scrolling-behind-a-sticky-header.html)
