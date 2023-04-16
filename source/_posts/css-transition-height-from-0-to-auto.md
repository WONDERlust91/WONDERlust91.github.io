---
title: css-transition height from 0 to auto
date: 2023-04-16 10:12:02
tags:
  - css
  - transition
  - animation
categories: css
---

If you are facing issues with the transition of a element from `height: 0` to `height: auto`, resulting in a jerky or stuck animation, there is a simple solution to this problem.

The problem arises because the final height of the element is unknown as it depends on the content inside it. CSS transitions work by calculating the difference between the initial and final states of a property and animating that difference over a specified duration. However, since the final height of the element is not known in advance, the browser cannot calculate the difference and therefore cannot create a transition between the two states.

<!-- more -->

## max-height

One solution to this problem is to use `max-height` instead of `height`. Set a value for `max-height` that is larger than the actual height of the element. The transition will be smooth because the browser knows the final `max-height` of the element. The `height` of the element can still be `auto`.

However, be sure not to set the `max-height` value too high. The larger the value is, the faster the animation will be. For example, if the actual `height` of the element is `500px`, but you set `max-height` to `5000px`. The transition will animate from 0 to 5000 in duration time. This means that the `500px` of `height` will only use one fifth of duration time to animate. Therefore, it's better to set the `max-height` value as close to the actual height of the element as possible to ensure that transition's duration is accurate.

```css
.start {
  overflow: hidden;
  max-height: 0;
  transition: max-height 0.5s ease-out;
}
.end {
  /* actual height is less than 500px */
  max-height: 500px;
}
```

<div>
  <style>
    .transition-height-from-0-to-auto__demo__trigger:hover + .transition-height-from-0-to-auto__demo__content {
      max-height: 150px;
    }
    .transition-height-from-0-to-auto__demo__content {
      overflow: hidden;
      max-height: 0;
      transition: max-height 0.5s ease-out;
    }
  </style>
  <p class="transition-height-from-0-to-auto__demo__trigger">hover to show</p>
  <ul class="transition-height-from-0-to-auto__demo__content">
    <li>content 1</li>
    <li>content 2</li>
    <li>content 3</li>
    <li>content 4</li>
  </ul>
</div>
<br />

> References
>
> [How can I transition height: 0; to height: auto; using CSS? - stackoverflow](https://stackoverflow.com/questions/3508605/how-can-i-transition-height-0-to-height-auto-using-css?page=1&tab=scoredesc#tab-top)
