---
title: javascript在页面中加载markdown文件
date: 2019-07-01 13:08:44
tags: markdown
categories: javascript
---

## 依赖库及其安装

Marked.js 将markdown语法的字符串解析成为html的库

raw-loader webpack插件，将.md文件做为文本引入

highlight.js 使页面上代码实现高亮的库

安装Marked.js与highlight.js依赖，将raw-loader安装为开发环境依赖

```shell
npm install marked highlight.js
npm install --save-dev raw-loader
```

<!-- more -->

## 使用

该文档基于vue-cli3生成的项目，故webpack配置项放在vue.config.js中

### 1. 配置raw-loader

在项目根目录新建vue.config.js，并做如下配置

```javascript
module.exports = {
  configureWebpack: {
    module: {
      rules: [
        {
          test: /\.md%/i,
          use: "raw-loader"
        }
      ]
    }
  }
};
```

配置完成后，就可以在项目中引入后缀为md的文件了

### 2.将.md文件载入页面

由于公司项目基本都在使用iview的UI框架，iview对浏览器默认样式进行了重置，所以需要重新设定诸如h1、h2、表格等css样式，这里我将重设的样式写在[md.less](../files/md.less)中；使用highlight.js时也需要引入选择的主题样式，这里我使用了github风格的代码样式。

```html
<template>
  <!-- 使用v-html绑定解析出的html代码，将所有为markdown重写的css样式放在md-container类下，不污染全局样式 -->
  <article class="md-container" v-html="mdHTML"></article>
</template>

<script>
// 载入md文件
import md from "@/../README.md";
// 引入marked.js
import marked from "marked";
// 引入highlight.js
import hljs from "highlight.js";
// 引入github代码样式
import "highlight.js/styles/github.css";
// 引入重写的h1、h2、table等css样式
import "./md.less";
export default {
  name: "md-example",
  data() {
    return {
      mdHTML: null
    }
  },
  mouted() {
    // 设置marked
    marked.setOptions({
      // 代码区域高亮
      highlight: function(code) {
        return `<pre class="hljs"><code>${hljs.highlightAuto(code).value}</code></pre>`;
      },
      // md中的空行识别为分段，默认为false
      breaks: true,
      // github flavored markdown 解析为github风格的markdown，默认为true，可不设置
      gfm: true,
      // 若表格项为true且gfm项也为true，则以github方式解析表格，默认为true，可不设置
      tables: true
    });
    // 将解析后的html文本赋值给mdHTML
    this.mdHTML = marked(md);
    // 也可以将设置marked与解析赋值合并为一步
    this.mdHTML = marked(md, {
      highlight: function(code) {
        return `<pre class="hljs"><code>${hljs.highlightAuto(code).value}</code></pre>`;
      },
      breaks: true,
      gfm: true,
      tables: true
    });
  }
}
</script>
```

这样就可以在页面上加载出md文件了，如果一些样式上还有不满意，可以自行调整[md.less](../files/md.less)样式文件以及选择不同的highlight主题。
