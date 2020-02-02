---
title: javascript 数组笔记
date: 2020-01-30 20:38:39
categories: javascript
---

ECMAScript 数组的每一项可以保存任何类型的数据；而且大小是可以动态调整的，即可以随着数据的添加自动增长以容纳新增数据。

<!--more-->

## 创建数组

### 构造函数

```js
var colors = new Array() // []
colors = new Array(10) // [empty × 10]
colors = new Array('red', 'blue', 'green') // ["red", "blue", "green"]
```

### 数组字面量表示法

```js
var colors = [] // 创建一个空数组
var colors = ['red', 'blue', 'green'] // 创建一个包含 3 个字符串的数组
var values = [1, 2] // 不要这样！这样会创建一个包含2或3项的数组
var options = [, , , , ,] // 不要这样！这样会创建一个包含5或6项的数组
```

## 读取和设置数组的值

```js
var colors = ['red', 'blue', 'green'] // 创建一个包含3个字符串的数组
alert(colors[0]) // 显示第1项
colors[2] = 'black' // 修改第3项
colors[3] = 'brown' // 新增第4项
```

## length 属性

```js
var colors = ['red', 'blue', 'green'] // 创建一个包含3个字符串的数组
alert(colors.length) // 显示数组长度
colors[colors.length] = 'black' // 在位置3添加一种颜色
colors[colors.length] = 'brown' // 在位置4再添加一种颜色
```

## 检测数组

```js
var array = []
if (Array.isArray(array)) {
  // 对数组执行某些操作
}
```

### Array.isArray Polyfill

如果是实际项目可以配置 `@babel/polyfill` 或者 `transform-runtime`，这不在本文的讨论范围

```js
if (!Array.isArray) {
  Array.isArray = function(arg) {
    return Object.prototype.toString.call(arg) === '[object Array]'
  }
}
```

## ES1 方法

- `Array.prototype.join`: `join()` 方法将一个数组（或一个类数组对象）的所有元素连接成一个字符串并返回这个字符串。如果数组只有一个项目，那么将返回该项目而不使用分隔符。**原始数组不会被改变**。
- `Array.prototype.reverse()`: `reverse()` 方法将数组中元素的位置颠倒，并返回该数组。**该方法会改变原数组**。
- `Array.prototype.sort()`: `sort()` 方法用原地算法对数组的元素进行排序，并返回数组。默认排序顺序是在将元素转换为字符串，然后比较它们的 UTF-16 代码单元值序列时构建的; sort 方法接收一个比较函数作为参数，如果第一参数应该位于第二个参数之前则返回负数，如果第一个参数应该位于第二个之后则返回一个正数。**该方法会改变原数组**。

```js
// join
var colors = ['red', 'blue', 'green']
console.log(colors.join()) // "red,blue,green"
console.log(colors.join('|')) // "red|blue|green"
console.log(colors.join('|')) // "red|blue|green"
console.log('red|blue|green'.split('|')) // ["red", "blue", "green"]

// reverse
var colors = [1, 2, 3, 4, 5]
console.log(colors.reverse()) // [5, 4, 3, 2, 1]

// sort
var colors = [1, 10, 3, 20, 5]
function sort(arr, asc = true) {
  return arr.sort((a, b) => {
    if (asc) {
      return a - b
    }
    return b - a
  })
}
console.log(sort(colors)) // [1, 3, 5, 10, 20]
console.log(sort(colors, false)) // [20, 10, 5, 3, 1]
```

- `String.prototype.split`(ES3): `split()` 方法使用指定的分隔符字符串将一个 String 对象分割成子字符串数组，以一个指定的分割字串来决定每个拆分的位置。

## ES3 方法

- `Array.prototype.push`: `push()` 方法可以接受任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度。**该方法会改变原数组**。
- `Array.prototype.pop`: `pop()` 方法从数组末尾移除最后一项，减少数组的 `length` 值，并返回该项。**该方法会改变原数组**。
- `Array.prototype.shift`: `shift()` 方法能够移除数组中的第一项并返回该项, 同时将数组长度减 1。**该方法会改变原数组**。
- `Array.prototype.unshift`: `unshift()` 方法能够在数组前端添加任意个项，并返回新数组的长度。**该方法会改变原数组**。
- `Array.prototype.concat()`: `concat()` 方法用于合并两个或多个数组。**原始数组不会被改变**，而是返回一个新数组。
- `Array.prototype.slice()`: `slice()` 方法返回一个新的数组对象，这一对象是一个由 `begin` 和 `end` 决定的原数组的浅拷贝（包括 `begin`，不包括 `end`）。**原始数组不会被改变**。
- `Array.prototype.splice()`: `splice()` 方法通过删除或替换现有元素或者原地添加新的元素来修改数组,并以数组形式返回被修改的内容。**此方法会改变原数组**。
  - **删除**: 可以删除任意数量的项，只需指定 2 个参数: 要删除的第一项的位置和要删除的项数。例如，`splice(0, 2)` 会删除数组中的前两项。
  - **插入**: 可以向指定位置插入任意数量的项，只需要提供 3 个参数：起始位置、0（要删除的项数）和要插入的项。如果要插入多个项，可以再传入任意多个项。例如，`splice(2, 0, "red", "green")` 会从当前数组的位置 2 开始插入字符串 `"red"` 和 `green`。
  - **替换**: 可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需要指定 3 个参数：起始位置、要删除的项数和要插入的任意数量的项。插入的项不必与删除的项数相等。例如，`splice(2, 1, "red", "green")` 会删除当前数组位置 2 的项，然后再从位置 2 开始插入字符串 `"red"` 和 `green`。

```js
// push、pop
var colors = ['red', 'blue', 'green']
var count = colors.push('black', 'brown') // 向末尾推入两项
console.log(count) // 5
var item = colors.pop() // 取得最后一项
console.log(item) // "brown"

// shift、unshift
var colors = ['red', 'blue', 'green']
var count = colors.unshift('black', 'brown') // 向开头推入两项
console.log(count) // 5
var item = colors.shift() // 取得第一项
console.log(item) // "red"

// concat
var colors = ['red', 'blue', 'green']
var colors1 = ['black', 'brown']
console.log(colors.concat(colors1)) // ["red", "blue", "green", "black", "brown"]

// slice
/**
 * 如果 `slice()` 方法的参数中有一个负数，则用数组长度加上该数来确定相应地位置。例如，在一个包含5项的数组上调用 `slice(-2, -1)` 与调用 `slice(3, 4)` 得到的结果相同。如果结束位置小于起始位置，则返回空数组。
 */
var colors = ['red', 'blue', 'green', 'black', 'brown']
var colors2 = colors.slice(1)
var colors3 = colors.slice(1, 4)

console.log(colors2) // ["blue", "green", "black", "brown"]
console.log(colors3) // ["blue", "green", "black"]

/**
 * splice() 方法始终都会返回一个数组，该数组中包含从原始数组中删除的项（如果没有删除任何项，则返回一个空数组）
 */
var colors = ['red', 'green', 'blue']
var removed = colors.splice(0, 1) // 删除第一项
console.log(colors) // ["green", "blue"]
console.log(removed) // ["red"] 返回的数组中只包含一项

removed = colors.splice(1, 0, 'yellow', 'orange') // 从位置 1 开始插入两项
console.log(colors) // ["green", "red", "orange", "blue"]
console.log(removed) // 返回的是一个空数组

removed = colors.splice(1, 1, 'red', 'purple') // 删除一项，增加两项
console.log(colors) // ["green", "red", "purple", "orange", "blue"]
console.log(removed) // yellow，返回的数组中只包含一项
```

## ES5 方法

- `Array.prototype.indexOf()`: `indexOf()` 方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。从数组的前面向后查找，从 `fromIndex` 处开始。
- `Array。prototype.lastIndexOf)()`: `lastIndexOf()` 方法返回指定元素在数组中的最后一个索引，如果不存在则返回-1。从数组的后面向前查找，从 `fromIndex` 处开始。

```js
var numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]
console.log(numbers.indexOf(4)) // 3
console.log(numbers.lastIndexOf(4)) // 5

console.log(numbers.indexOf(4, 4)) // 5
console.log(numbers.lastIndexOf(4, 4)) // 3

var person = { name: 'Nicholas' }
var people = [{ name: 'Nicholas' }]
var morePeople
/**
 * indexOf、lastIndexOf 在比较第一个参数与数组中的每一项时，会使用全等操作符
 */
console.log(people.indexOf(person)) // -1
console.log(morePeople.indexOf(person)) // 0
```
