---
title: vue3_vite_typescript_element-plus初探与踩坑总结
date: 2021-07-25 16:01:59
tags:
  - vue3
  - vite
  - typescript
  - element-plus
categories: vue3
---

平时公司都在用 react，最近正好一个项目原先的代码使用了 vue 框架，项目只是 demo 阶段，代码量还很少，而且 vue3 已发布接近一年了，就考虑在这个项目上切换 vue3 框架。

## 当下的 vue3 背景情况

自 2020 年 9 月 vue 正式发布 vue3 版本后，已接近一年时间，官方已经有了中文版 vue3 文档，通读官方文档并尝试后，感觉与 typescript 结合使用，并没有想象中那么丝滑。遂又去官方 github 上查询资料，发现 script setup 标签更适合与 typescript 结合使用。犹记得 2020 年底时 script setup 标签与 ref 语法糖还是引起了很大的争议的，而官方文档中却又都未提及。查询 rfcs 后发现 script setup 标签刚刚正式定版，在 3.1.3 版本中正式发布，在 3.2.0 版本中去除试验状态，成为正式的语法。而 ref 语法糖因为太过激进，语法与 js 差异太大，而被废除。

综合来看，当下 vue 官方文档对 vue3 的介绍重点还在于与 vue2 版本的兼容，vue2 的绝大部分语法，vue3 依然支持，vue3 只是在 vue2 的基础上增加了 setup 函数。与 typescript 的结合使用，也是基于 vue2 兼容加 setup 函数的基础上介绍，甚至在 props 定义类型时，还混合了 typescript 类型断言与 js 的类型构造函数，感觉有些别扭。而官方文档中尚未提及的 script setup 标签语法，才像是 vue3 不考虑 vue2 语法兼容性的功能完整释放，借助一系列 API，也可以与 typescript 有更好的结合使用体验。

<!-- more -->

## 构建工具 vite

vite 是 vue 的创始人尤雨溪在构建工具上的新尝试。时间来到 2021，似乎前端发展又进入了一个新的纪元，曾经的模块化与多浏览器兼容似乎才是构建工具要解决的核心问题，让开发人员更专注于业务功能，而不是把大量时间花费在处理不同浏览器的兼容问题上。而如今各大浏览器厂商标准在趋同，曾经的王者且是处理兼容问题最困难的浏览器 IE 已经退出了历史的舞台，而曾经扮演屠龙者的 Chrome 浏览器已经成长为新的龙王，微软也使用了 Chrome 内核开发新的浏览器，并与 Google 共同维护 Chrome 内核，那么兼容已经不再是最核心的问题了，兼容处理工具 Babel 也传出了要破产的传闻。至于模块化，浏览器已经原生支持了模块化，上个纪元的构建工具霸主 webpack 至今还未在正式版中支持 es 形式的打包，似乎已经开始走下坡路了。

2020 年的 JS 最具创意项目奖，颁发给了 snowpack 构建工具项目，而今年，尤雨溪也发布了新的构建工具 vite。构建工具似乎是前端变革的方向之一。vite 最大的特点是开发环境无打包，直接利用原生浏览器模块化，实现冷起动，热更新。生产环境打包使用了 rollup 而不是最新最快的 esbuild，主要是因为 esbuild 在处理代码分割与 css 方面还处于开发状态，未来不排除更换 esbuild 打包的可能。至于为什么还需要打包，则是因为原生 ESM 嵌套导入会导致额外的网络往返，即使是使用 HTTP/2 的情况下，在生产环境使用未打包的 ESM 仍然效率低下。

目前 vue3 官方文档中尚未将 vite 作为默认构建工具，还依然使用 vue-cli 作为脚手架，短期依然是 vite 与 vue-cli 并行，未来可能会使用 vite 作为默认的构建工具。

## 编辑器插件 volar

vue2 时代，用于编辑器 vue 单文件组件的插件 vetur 相信每一个人都不陌生，但由于 vue3 需要完整支持类型检查，而 vetur 因为历史包袱无法很好的支撑，故未来官方会放弃 vetur。

取而代之的是个人开发的 volar，未来官方插件也会基于 volar 去做，将 vetur 的好的功能整合进 volar 中。volar 实现了编辑器中的类型检查，而命令行也有一个工具叫 vue-tsc，也是 volar 的作者开发的，执行与 volar 同样的检查逻辑，既可以检查 vue 文件也可以检查 ts 文件，vue-tsc 已整合进入 vite 的 typescript 默认模板中。

## vue-router 与 vuex

适配 vue3 的 vue-router4 与 vuex4 都已经稳定，可以使用。

## 踩坑实践

### 不推荐使用 node.js 16 版本

当前 node.js 的长期支持版本为 14，对应 npm 版本为 6，而最新的 node.js 版本为 16，对应 npm 版本为 7，npm 7 对依赖的组织形式有较大的变革。大部分库尚示对 node.js 16 与 npm 7 进行兼容，极易出现错误，故不推荐使用。vite 初始安装时的 esbuild 错误就是因为 node 版本太新。

### vite 项目初始化完成后执行`npm i`报错 esbuild.exe ENOENT 4058

vite 使用了 esbuild 依赖，而 esbuild 在 node.js 16、npm 7 环境下自动安装失败，需要在 vite 项目当前目录下执行`node node_modules/esbuild/install.js`进行手动安装。

使用 node.js 14 版本不会遇到此错误。

> [vite 搭建项目提示 Error: spawn .../esbuild.exe ENOENT errno: -4058](https://blog.csdn.net/weixin_45442869/article/details/118499967)

### vue-router doesn't provide an export named 'default'

vue-router 没有默认导出了，使用`import Router from 'vue-router'`就会报错，而应该使用`import * as Router from 'vue-router'`。

由于 ES6 的 export default 并不是解构语法，而是相当于声明了一个 default 变量，只能支持浏览器端，node 端的支持是通过打包器的转译实现的，而如果你去支持 node 端，又会造成浏览器端的 import default 为空的问题。所以越来越多的库在经过使用 ES6 与 typescript 改写后，都放弃了 export default 的导出形式，如 echarts5。当然如果开启了 tsc 或 babel 的 esModuleInterop 选项，也可以以 import default 的形式引入非 export default 形式的库，但这要求开发者开启配置，这实际上是不可控的。

> [vite The requested module vue-router?](https://stackoverflow.com/questions/66971106/vite-the-requested-module-vue-router)
>
> [深入解析 ES Module（二）：彻底禁用 default export](https://zhuanlan.zhihu.com/p/97335917)

### vite typescript 模板结合 element-plus 使用打包报错 Cannot find name xxx

原因是 element-plus 未通过 vite typescript 模板项目 build 命令中`vue-tsc --noEmit`的类型检查，element-plus 尚未修复该问题，故需要使用`--skipLibCheck`参数，或在 tsconfig 文件中配置`skipLibCheck`为`true`。

> [build error（vite、vue3、typescript、element plus）](https://github.com/element-plus/element-plus/issues/1852)

### element-plus 默认样式覆盖组件中样式，打包 css 顺序错误

将 element 的全局样式从 main.ts 中抽出，放入 App.vue 中

> [vue-cli 脚手架搭建的项目，打包的 css 顺序有问题，求教该怎么修改打包配置？- 静修丶的回答](https://segmentfault.com/q/1010000010279605)

### 在 script 标签中引入类型报错 XXX only refers to a type, but is being used as a value here

在 vue3 的 script 标签中需要区分类型引入和值的引入。引入类型`import type { XXX } from 'xxx';`，引入值`import { XXX } from 'xxx';`。

> [typescript: error TS2693: 'Promise' only refers to a type, but is being used as a value here.](https://github.com/johnsoncodehk/volar/issues/165)

---

UPDATE: 2021-08-11 最新的 vue3.2.X 版本中，已修复该问题，引入类型时无需特别使用`import type`语法，直接 import 即可。

> [script-setup: Types need to be imported separately with import type ....](https://github.com/vuejs/vue-next/issues/3183)

### vue3 使用 typescript 定义多重 props 类型（不推荐使用 defineComponent 形式，使用 typescript 需要和 js 类型构造函数混用，推荐使用 script setup 形式）

```html
<script>
  // vue js 定义多个props类型
  import { defineComponent, PropType } from "vue";
  const Component = defineComponent({
    props: {
      multipleType: [Object, Array],
    },
  });
</script>
<script lang="ts">
  // vue ts 定义多个props类型，错误写法
  const Component = defineComponent({
    props: {
      // 导致编译报错
      multipleType: [Object as PropType<{[key: string]: any}>, PropType<Array as string[]>]
    }
  });
</script>
<script lang="ts">
  // vue ts 定义多个props类型，正确写法
  const Component = defineComponent({
    props: {
      multipleType: [Object, Array] as PropType<{[key: string]: any} | string[]>;
    }
  })
</script>
```

从上面例子可以看出定义多重类型的 props 需要使用 PropType 泛型辅助，而且只能写在最外层 as 一次，还结合了 js 原生的类型构造函数，很不优雅。推荐使用 script setup 形式，符合原生 ts 写法。

```html
<script lang="ts" setup>
  const props = defineProps<{
    multipleType: { [key: string]: any } | string[];
  }>();
</script>
```

> [Vue3 TypeScript props with multiple types](https://stackoverflow.com/questions/64279223/vue3-typescript-props-with-multiple-types)

### props 声明联合类型中有 null 时报错 Right-hand side of 'instanceof' is not an object

```html
<script lang="ts" setup>
  const props = defineProps<{
    // 这样声明类型导致报错
    test: string | null;
  }>();
</script>
```

只要联合类型中带有 null 就会在编译时报错。在网络查询未找到答案，vue 编译也提示是一个未捕获的错误。尝试以下方法可解决：

```html
<script lang="ts" setup>
  // 第一种解决方法：用括号包裹联合类型，并在这一行禁用prettier默认格式化，防止括号被默认格式化去除
  const props = defineProps<{
    // prettier-ignore
    test: (string | null);
  }>();
  // 第二种方法：从ts文件中引入类型
  // myType.ts
  // export type MyType = string | null;
  import type { MyType } from "myType.ts";
  const props = defineProps<{
    test: MyType;
  }>();
</script>
```

---

UPDATE: 2021-08-11 最新的 vue3.2.x 版本中该问题已经修复，无需使用括号与 prettier-ignore。

### props 使用 extends 关键字继承的接口类型报错

```html
<script lang="ts" setup>
  // 基础类型
  interface BaseType {
    foo: string;
  }
  // 继承类型
  interface PropsType extends BaseType {
    bar: string;
  }
  // 使用继承类型定义的props，会报错
  const props = defineProps<PropsType>();
  // 使用普通类型props，正常
  const props = defineProps<BaseType>();
</script>
```

使用了 extends 关键字经过继承的类型后，会导致 vue 报错，查阅官方 issue 后发现官方不推荐从各处引入类型，期望把类型写在一个文件中，故无法使用 extends 继承的类型。不过虽然在 3.2.23 版本中，还会报错，但似乎使用 extends 关键字的修改代码已经合并到了分支中。

> [feat(sfc): support using extends interface with defineProps()](https://github.com/vuejs/vue-next/pull/4512)

## vue3 全面示例

值得重点关注的特性：

1. script setup 标签
2. style 动态变量注入
3. Provide/Inject
4. 调试生命周期钩子函数

### 父组件代码示例

```html
<!-- parent-component.vue -->
<template>
  <div class="var1">parent var1：{{ var1 }}</div>
  <div>parent obj1：{{ obj1 }}</div>
  <div>parent key1：{{ key1 }}</div>
  <div>parent key2：{{ key2 }}</div>
  <child-component ref="childInstance" v-model="childModel" @click-prop1="onChildProp1Click" />
</template>

<script lang="ts" setup>
  import ChildComponent from "./child-component.vue";
  // setup中相当于原beforeCreate与Created阶段，故生命周期中无这两个阶段
  import {
    ref,
    reactive,
    toRefs,
    provide,
    onMounted,
    onBeforeMount,
    onBeforeUpdate,
    onUpdated,
    onBeforeUnmount,
    onUnmounted,
    onActivated,
    onDeactivated,
    onErrorCaptured,
    onRenderTracked,
    onRenderTriggered,
  } from "vue";
  // ref声明的变量为响应式变量，值改变会触发其他组件刷新，需要通过.value属性访问其值，
  // ref更适合用于声明基础类型的变量
  const var1 = ref<string>("red");
  console.log(var1.value);
  // reactive声明的变量为响应式变量，值改变会触发其他组件刷新，无需通过.value属性访问，
  // reactive更适合声明对象类型的变量
  const obj1 = reactive<{
    key1: string;
    key2: number;
  }>({ key1: "1", key2: 2 });
  console.log(obj1.key1);
  // 对obj1的解构会使之丧失响应性，故vue提供了toRefs方法，将reactive转换为ref
  const { key1, key2 } = toRefs(obj1);

  // 使用provide将var1传递给子孙组件使用，无需层层传递
  provide("parentVar1", var1.value);

  // 子组件实例ref，单文件组件需要使用 InstanceType 泛型与 typeof 定义组件类型，
  // 注意这里不要用联合null类型，默认什么都不填就好（默认为undefined），否则导致volar类型错误（volar版本0.26.15）
  const childInstance = ref<InstanceType<typeof ChildComponent>>();
  // 子组件v-model变量
  const childModel = ref<number>(0);
  // 子组件prop1点击事件
  const onChildProp1Click = (value: string) => {
    console.log(value);
    // 操作子组件实例上暴露出的方法
    if (childInstance.value) {
      childInstance.value.minusComponentNumber();
    }
  };
  /**
   * 生命周期钩子函数，由于setup就在beforeCreate与created阶段运行，
   * 所以不需要显式定义这两个钩子函数；
   */
  onBeforeMount(() => {
    console.log("页面挂载前");
  });
  onMounted(() => {
    console.log("页面挂载后");
  });
  onBeforeUpdate(() => {
    console.log("页面更新前");
  });
  onUpdated(() => {
    console.log("页面更新后");
  });
  onActivated(() => {
    console.log("keep-alive缓存组件激活，服务端渲染不调用该钩子");
  });
  onDeactivated(() => {
    console.log("keep-alive缓存组件停用，服务端渲染不调用该钩子");
  });
  onBeforeUnmount(() => {
    console.log("页面卸载前");
  });
  onUnmounted(() => {
    console.log("页面卸载后");
  });

  /**
   * 这三个钩子函数用于错误处理与调试，与前面生命周期不同，这三个钩子函数有参数传入
   */
  // 该钩子捕获一个来自子孙组件的错误时被调用，
  // 接收三个参数：错误对象、发生错误的组件实例，包含错误来源的字符串，
  // 该钩子可以返回false以阻止错误向上传播
  onErrorCaptured((err, instance, info) => {
    return false;
  });
  // 跟踪虚拟 DOM 重新渲染时调用。
  // 钩子接收 debugger event 作为参数。此事件告诉你哪个操作跟踪了组件以及该操作的目标对象和键。
  onRenderTracked((event) => {});
  // 当虚拟 DOM 重新渲染被触发时调用。
  // 和 renderTracked 类似，接收 debugger event 作为参数。此事件告诉你是什么操作触发了重新渲染，以及该操作的目标对象和键。
  onRenderTriggered((event) => {});
</script>

<style lang="scss" scoped>
  .var1 {
    // 使用v-bind方法，可以直接绑定script中的变量，相比jss方案，几乎没有运行时成本
    color: v-bind(var1);
  }
  // 使用/deep/与>>>语法深度选择scoped样式已弃用，需要使用::v-deep方法包裹样式类名
  ::v-deep(.child-prop2) {
    color: blue;
  }
</style>
```

### 子组件代码示例

```html
<!-- child-component -->
<template>
  <div>
    <div
      @click="
        () => {
          emits('clickProp1', '您点击了child的prop1');
        }
      "
    >
      child prop1：{{ props.prop1 }} 点击使componentNumber减1
    </div>
    <div
      @click="
        () => {
          emits('update:modelValue', modelValue + 1);
        }
      "
    >
      modelValue：{{ modelValue }} 点击加1
    </div>
    <div class="child-prop2">prop2：{{ props.prop2 }}</div>
    <div>parent-var1：{{ parentVar }} 由父组件inject</div>
    <div>10倍的prop2：{{ tenTimesProp2 }}</div>
    <div>componentNumber：{{ componentNumber }} 父组件操作子组件暴露的方法修改该变量</div>
  </div>
</template>

<script lang="ts" setup>
  import { ref, computed, watch, watchEffect, useSlots, useAttrs, inject } from "vue";
  // 若不需要设置默认值，直接这样定义props即可
  /* const props = defineProps<{
  modelValue: number;
  prop1: string;
  prop2: number;
}>(); */
  // 若要设置默认值，则需要使用withDefaults方法直接包裹defineProps作为第一个参数，要设置的默认值为第二个参数
  // 注意withDefaults要直接包裹defineProps，而不能传入defineProps的返回值
  const props = withDefaults(
    defineProps<{
      modelValue: number;
      prop1?: string;
      prop2?: number;
    }>(),
    {
      prop1: "prop1",
      prop2: 1,
    }
  );
  // 定义事件
  const emits = defineEmits<{
    // 普通事件
    (e: "clickProp1", value: string): void;
    // v-model的更新事件
    (e: "update:modelValue", value: number): void;
  }>();

  // 注入祖辈组件传递的provider变量
  const parentVar = inject<string>("parentVar1");

  // 获取模板
  const slots = useSlots();
  // 获取属性
  const attrs = useAttrs();

  const componentNumber = ref<number>(0);
  const minusComponentNumber = () => {
    componentNumber.value--;
  };
  // vue3中不再默认暴露组件实例上的方法，需要使用defineExpose将组件上的方法显示定义暴露出去给父组件使用
  defineExpose({
    minusComponentNumber,
  });
  // computed用法
  const tenTimesProp2 = computed(() => {
    return props.prop2 * 10;
  });
  /**
   * 监听props，由于watch的第一个参数必需为响应式的对象，
   * 而props的值在组件内部通常不是响应式的（prop本身是一个proxy但内部的值不一定是响应式，当然也不排除props的值传为一个响应式变量，这里仅讨论props的值非响应式的情况），
   * 所以监听props需要使用函数返回值的形式
   */
  watch(
    () => props.prop1,
    (newValue, oldValue) => {
      console.log(`prop1改变了，新的值为${newValue}，上次值为${oldValue}`);
    }
  );
  /**
   * 监听ref，不使用.value，直接使用响应式对象本身作为第一个参数
   */
  watch(componentNumber, (newValue, oldValue) => {
    console.log(`componentNumber改变了，新的值为${newValue}，上次值为${oldValue}`);
  });
  /**
   * 监听多个数据源，可以使用数组
   */
  const firstName = ref("");
  const lastName = ref("");

  watch([firstName, lastName], (newValues, prevValues) => {
    console.log(newValues, prevValues);
  });

  firstName.value = "John"; // logs: ["John", ""] ["", ""]
  lastName.value = "Smith"; // logs: ["John", "Smith"] ["John", ""]
  // 如果在同一个方法里同时改变这些被侦听的数据源，侦听器也只会执行一次
  // 当然也可以更改watch的设置 flush: 'sync'，为每次更改都强制触发侦听；默认为'pre'即收集所有变化后在beforeUpdate阶段触发；还可以设置为'post'，即在收集所有变化后在updated阶段触发
  const changeValues = () => {
    firstName.value = "John";
    lastName.value = "Smith";
    // 打印 ["John", "Smith"] ["", ""]
  };
  /**
   * 侦听响应式对象或数组
   * 由于对象与数组都是引用类型，侦听他们时，当前值与上次值都始终以当前值返回。
   * 因此对于嵌套对象或数组，如果无需用到上次值，则可以使用deep选项监听，否则最好返回他们的深拷贝，
   * 如果响应式对象中还嵌套了响应式对象，这种响应式是基于proxy实现的，proxy性能较差，数据量较大，还有遍历操作时，proxy就会成为性能瓶颈，因此使用深拷贝也可以使proxy变为普通对象，提升性能，
   * 综上，深拷贝是侦听响应式对象或数组的最佳实践
   */
  // 浅拷贝侦听一维数组
  const numbers = reactive([1, 2, 3, 4]);
  watch(
    () => [...numbers],
    (numbers, prevNumbers) => {
      console.log(numbers, prevNumbers);
    }
  );
  numbers.push(5); // logs: [1,2,3,4,5] [1,2,3,4]
  // deep侦听嵌套对象，此时本次值与上次值一致
  const state = reactive({
    id: 1,
    attributes: {
      name: "",
    },
  });
  watch(
    () => state,
    (state, prevState) => {
      console.log("not deep", state.attributes.name, prevState.attributes.name);
    }
  );
  watch(
    () => state,
    (state, prevState) => {
      console.log("deep", state.attributes.name, prevState.attributes.name);
    },
    { deep: true }
  );
  state.attributes.name = "Alex"; // 日志: "deep" "Alex" "Alex"
  // 深拷贝侦听嵌套对象，此时可以区分本次值与上次值
  // 这里深层都是基本类型，若深层为响应式的引用类型且数据量很大（如格点数据数组），同时还有遍历操作时（如根据数据画色斑），响应式的实现proxy会非常耗性能，因此深拷贝还将响应式转换为了普通类型，无形中降低了性能损耗
  import _ from "lodash";
  const state = reactive({
    id: 1,
    attributes: {
      name: "",
    },
  });
  watch(
    () => _.cloneDeep(state),
    (state, prevState) => {
      console.log(state.attributes.name, prevState.attributes.name);
    }
  );
  state.attributes.name = "Alex"; // 日志: "Alex" ""
  /**
   * watchEffect与watch区别
   * 1. watchEffect无需传入侦听源，会自动判断回调函数中的响应式对象作为侦听源; watch需要传入响应式对象作为侦听源;
   * 2. watchEffect会立即执行回调函数，并在依赖更新时重新调用; watch仅在依赖更新时调用回调函数;
   * 3. watchEffect无法访问侦听状态的先前值和当前值; watch可以访问被侦听状态的先前值和当前值;
   */
  watchEffect(() => {
    console.log(`componentNumber改变了，其值为${componentNumber.value}`);
  });
</script>

<style lang="scss" scoped>
  // 虽然写了scoped，但样式会被外层覆盖
  .child-prop2 {
    color: green;
  }
</style>
```

> 参考资料
>
> [script setup RFCs](https://github.com/vuejs/rfcs/pull/227#issuecomment-870105222)
>
> [active-rfcs 0040-script-setup](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0040-script-setup.md)
>
> [vue watch api](https://v3.cn.vuejs.org/api/computed-watch-api.html#watch)
>
> [vue typescript template ref](https://v3.cn.vuejs.org/guide/typescript-support.html#%E4%B8%BA%E6%A8%A1%E6%9D%BF%E5%BC%95%E7%94%A8%E5%AE%9A%E4%B9%89%E7%B1%BB%E5%9E%8B)
>
> [How do I use /deep/ or >>> or ::v-deep in Vue.js?](https://stackoverflow.com/questions/48032006/how-do-i-use-deep-or-or-v-deep-in-vue-js)
>
> [How to Watch Props Change with Vue Composition API / Vue 3?](https://stackoverflow.com/questions/59125857/how-to-watch-props-change-with-vue-composition-api-vue-3)
