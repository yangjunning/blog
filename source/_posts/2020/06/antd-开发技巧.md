---
title: antd 开发技巧
date: 2020-06-05 16:59:43
tags:
---

<!--more-->

## 让Input 组件自动获取焦点

添加 autoFocus 属性：

```jsx
<Input autoFocus />
```

- https://github.com/ant-design/ant-design/issues/2952
- https://stackoverflow.com/questions/28889826/how-to-set-focus-on-an-input-field-after-rendering

## Table 表格

### table组件多选时，会选中下一页的表格同行数据的解决办法

类似选中某页的几行数据，在删除等操作后其他页面的表格或者删除后本表格的同行页为选中状态。这是因为没有区分每页表格的`id`。解决办法是给 Table 加上 `rowKey`

