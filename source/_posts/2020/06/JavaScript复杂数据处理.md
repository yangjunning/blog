---
title: JavaScript复杂数据处理
date: 2020-06-11 09:29:52
tags:
---

## 根据一个数组过滤另一个数组

- Array.prototype.includes
- Array.prototype.forEach

```js
//筛选数组：[{id:2},{id:3}]
let ids = [2, 3];
let arr = [{
    id: 1
  },
  {
    id: 2
  },
  {
    id: 3
  }
];
let newArr = [];

arr.forEach(item => {
  if (ids.includes(item.id)) {
    newArr.push(item);
  };
})
console.log(newArr); //输出：[{id:2},{id:3}]
```

<!--more-->
