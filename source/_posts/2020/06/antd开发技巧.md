---
title: antd开发技巧
date: 2020-06-05 16:59:43
categories:
  - [前端开发,React]
tags:
  - antd
---

<!--more-->

## 让Input 组件自动获取焦点

添加 `autoFocus` 属性：

```jsx
<Input autoFocus />
```

- https://github.com/ant-design/ant-design/issues/2952
- https://stackoverflow.com/questions/28889826/how-to-set-focus-on-an-input-field-after-rendering

## Table 表格

### table组件多选时，会选中下一页的表格同行数据的解决办法

> 解决办法来自：https://blog.csdn.net/weixin_41606276/article/details/99312155

类似选中某页的几行数据，在删除等操作后其他页面的表格或者删除后本表格的同行页为选中状态。这是因为没有区分每页表格的`id`。解决办法是给 Table 加上 `rowKey`

### defaultExpandAllRows={true} 刷新后不展开问题

> 解决办法来自：https://github.com/ant-design/ant-design/issues/4145#issuecomment-287954977

借助了key改变自动变成新的component，虽然可以解决默认问题。。但总觉得不是key正确的用法

```jsx
<Table
  key={`table-${this.state.data && this.state.data.length}`}
  defaultExpandAllRows
/>
```
