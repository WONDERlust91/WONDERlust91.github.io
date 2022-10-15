---
title: vscode用户配置与vue项目通用配置
date: 2018-10-20 23:23:27
tags: vscode vue iview
categories: vscode
---

## 用户文件配置

```JSON
{
    // 设置编辑器的tabSize为2
    "editor.tabSize": 2,
    // 关闭基于文件自动检测tabSize与insertSpaces
    "editor.detectIndentation": false,
    // 使用prettier格式化vue中的html
    "vetur.format.defaultFormatter.html": "prettier",
    // 不使用vetur的简化版eslint-plugin-vue检查template中的html，因为会报x-invalid-end-tag
    "vetur.validation.template": false,
    // 文件路径改变后自动更新import
    "typescript.updateImportsOnFileMove.enabled": "always",
    "javascript.updateImportsOnFileMove.enabled": "always",
    // 防止api-extractor init出的json文件中有注释而报错
    "files.associations": {
      "*.json": "jsonc"
    },
}
```

## ESlint 的 rules 文件配置

使用配置前确保项目中使用 npm 安装了 eslint，eslint-plugin-vue 等包，以及 vetur，prettier 等 vscode 插件，现在 3.3 版本的 vue cli 已安装 eslint-plugin-vue。

```javascript
module.exports = {
  root: true,
  env: {
    node: true,
  },
  extends: ["plugin:vue/essential", "@vue/prettier"],
  rules: {
    "no-console": process.env.NODE_ENV === "production" ? "error" : "off",
    "no-debugger": process.env.NODE_ENV === "production" ? "error" : "off",
    // 不检查iview等UI框架的“x-invalid-end-tag”错误，使用vetur简化版禁用不全，应安装eslint-plugin-vue
    "vue/no-parsing-error": [
      2,
      {
        "x-invalid-end-tag": false,
      },
    ],
    /* // 现已使用两个空格缩进，下面4空格设置废弃
        // 对vue文件中的html缩进检查设置为4
        "vue/html-indent": [
            "error",
            4,
            {
                "attribute": 1,
                "closeBracket": 0,
                "alignAttributesVertically": true,
                "ignores": []
            }
        ],
        // 将prettier的缩进检查设置为4
        "prettier/prettier": [
            "error",
            {
                tabWidth: 4
            }
        ] */
  },
  parserOptions: {
    parser: "babel-eslint",
  },
};
```

---

默认已经安装 vetur 插件
