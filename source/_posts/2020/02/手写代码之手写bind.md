---
title: 手写代码之手写bind
date: 2020-02-19 15:28:58
categories:
  - [前端开发, JavaScript]
tags:
  - 手写代码
---

`bind` 是在 ES5 加入的语法，具体如何使用 MDN 都有。但有一点需要额外注意一下：`bind` 会返回一个新的函数。也就是说：它会修改 `this` 的指向，并返回新的函数体。

<!--more-->

## 实现思路

1. 改变 `this` 指向: 需要使用 `apply` 或 `call`
2. 返回新的函数体: 函数作为值返回

## 实现

一个简单的 `bind()` 一个环境。并返回一个在给定环境中调用给定函数的函数，并且将所有参数原封不动传递进去。语法如下：

```js
Function.prototype.bind1 = function(context) {
  var that = this
  return function() {
    that.apply(context, arguments)
  }
}
```

使用方式和原生 `bind()` 一样：

```js
var person = { name:"杨俊宁" }
function sayHi(name) {
  console.log(this, name)
}
var pSay = sayHi.bind1(person)
```
