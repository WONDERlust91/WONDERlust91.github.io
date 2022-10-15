---
title: iview模态框设置loading后表单验证失败无法清除loading状态
date: 2019-02-12 16:33:31
tags:
  - iview
  - modal
  - 模态框
categories: iview
---

# iview 模态框设置 loading 后表单验证失败无法清除 loading 状态

在使用 iview 模态框过程中我们使用确定按钮异步提交信息，提交成功后才关闭模态框，但我们发现若无法通过表单验证，禁止提交时，确定按钮的 loading 状态无法清除，无法修改表单后再次提交信息。官方文档中也没有关于这一点的说明，查阅官方 issue 后发现了[解决方案](https://github.com/iview/iview/issues/597)。即先将loading状态设置为false，再使用this.$nextTick在下次将loading状态设置为true。

```html
<template>
  <Modal :loading="modalLoading" @on-ok="submitForm">
    <form ref="form"></form>
  </Modal>
</template>
<script>
  export default {
    name: "test",
    data() {
      return {
        modalLoading: true
      };
    },
    methods: {
      // 改变loading状态函数
      changeLoading() {
        // 将loading状态设置为关闭
        this.modalLoading = false;
        // 下次模态框加载时重置loading状态为true
        this.$nextTick(() => {
          this.modalLoading = true;
        });
      },
      // 点击确定执行的函数
      submitForm() {
        // iview表单验证
        this.$refs.form.validate(valid => {
          if (valid) {
            console.log("表单验证成功，异步执行确定请求");
          } else {
            // 表单验证失败时重置loading状态
            this.changeLoading();
          }
        });
      }
    }
  };
</script>
```
