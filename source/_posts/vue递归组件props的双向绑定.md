---
title: vue递归组件props的双向绑定
date: 2019-07-11 09:11:44
tags:
  - 递归组件
  - 双向绑定
categories: vue
---

## 自定义组件 prop 的双向绑定

在 vue 中当我们要用到双向绑定的时候，第一反应是使用 v-model 属性，但 v-model 属性只能用在部分表单相关的组件上，如 input、select、textarea。如果我们在自定义的组件传递的 prop 上也想达到双向绑定的效果该如何做呢？答案是使用[.sync 修饰符](https://cn.vuejs.org/v2/guide/components-custom-events.html#sync-修饰符)。

.sync 修饰符实质上是 v-bind 与 update 事件的语法糖，我们通过在子组件内给父组件传递事件来更新父组件传给子组件的 prop，以实现双向绑定。

```html
<!-- 在父组件内 -->
<template>
  <custom-component :foo="bar" @update:foo="bar = $event"></custom-component>
  <!-- 上面的写法等同于 -->
  <custom-component :foo.sync="bar"></custom-component>
</template>
```

```html
<!-- 在子组件内 -->
<script>
  export default {
    props: ["foo"],
    methods: {
      updatePropFoo(newFoo) {
        this.$emit("update:foo", newFoo);
      }
    }
  };
</script>
```

<!-- more -->

## 计算属性的 setter

[计算属性](https://cn.vuejs.org/v2/guide/computed.html#计算属性的-setter) computed 默认只有 getter，我们也可以提供一个 setter。

```javascript
export default {
  data: {
    firstName: "Tom",
    lastName: "Wu"
  },
  computed: {
    fullName: {
      get: function() {
        return this.firstName + " " + this.lastName;
      },
      set: function(newValue) {
        const newName = newValue.split(" ");
        this.firstName = newName[0];
        this.lastName = newName[newName.length - 1];
      }
    }
  }
};
```

此时我们修改 this.fullName = "Mark Chen"时，setter 会被调用，this.firstName 与 this.lastName 也会被相应更新。

## 递归自定义组件prop的双向绑定

前面两个小节的知识是我们实现递归自定义组件prop双向绑定的基础。因为通过了解.sync的原理，我们知道双向绑定一定是通过事件将更新的值传递到父组件中去修改，子组件并不会直接修改prop。而递归子组件时，我们需要将prop再次传入子组件，那么最深层的子组件就会在次深层子组件中修改prop，而不是一直将事件传递到父组件去，这样就会报错了。而为了解决这个问题，我们在递归的子组件中不使用父组件传递进来的prop本身，而使用由prop创建出的computed属性，再在computed的setter中继续向上层组件传递更新prop的事件，这样就实现了递归组件prop的双向绑定。

```html
<!-- 父组件中 -->
<template>
  <div>
    <custom-component :foo.sync="bar"></custom-component>
  </div>
</template>
<script>
import CustomComponent from "./CustomComponent.vue";
export default {
  name: "father",
  components: { CustomComponent },
  data() {
    return {
      bar: ""
    }
  }
}
</script>
```

```html
<!-- 在子组件中 -->
<template>
  <div>
    <div @click="updateFoo"></div>
    <!-- 递归组件，使用计算属性 -->
    <custom-component v-if="condition==true" :foo.sync="computedFoo"></custom-component>
    <div v-else>递归终结条件</div>
  </div>
</template>
<script>
export default {
  name: "CustomComponent",
  props: ["foo"],
  computed: {
    // 递归组件中更新foo
    computedFoo: {
      get: function() {
        return this.foo;
      },
      set: function(newValue) {
        this.$emit("update:foo", newValue);
      }
    }
  },
  methods: {
    // 更新foo
    updateFoo(newFoo) {
      this.$emit("update:foo", newFoo);
    }
  }
}
</script>
```

这样我们就实现了递归自定义组件prop的双向绑定。
