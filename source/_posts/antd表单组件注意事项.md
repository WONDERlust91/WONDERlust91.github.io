---
title: antd表单组件注意事项
date: 2021-05-03 19:22:31
tags:
  - antd
  - form
categories: antd
---

## 表单 Form.Item 只会对它的直接子元素绑定表单功能

例如 Form.Item 直接包裹了 Input/Select。若是在 Input 等控件前后还有文案或样式装点，抑或是控件是经过条件判断后再直接包裹的，都无法自动绑定表单功能。还需要使用内嵌的 Form.Item 来完成表单功能。可以在 Form.Item 上添加 noStyle 属性作为无样式的绑定组件。

```tsx
// 有文案或样式的情况
<Form.Item label="Field">
  <Form.Item name="field" noStyle><Input /></Form.Item> // 直接包裹才会绑定表单
  <span>description</span>
</Form.Item>
// 有条件判断的情况
<Form.Item label="Field">
  {condition ? <Form.Item noStyle><Input /></Form.Item> : <Form.Item noStyle><Select /></Form.Item>}
</Form.Item>
```

> 参考[官方文档](https://ant.design/components/form-cn/#components-form-demo-complex-form-control)
