---
title: 【译】在 React Native TypeScript 项目中使用 defaultProps
categories:
  - [移动开发, React Native]
tags:
  - 翻译
  - defaultProps
  - TypeScript
date: 2019-06-03 15:55:52
---

随着时间的推移，TypeScript 已经改进了 `defaultProps` 相关的类型检查。本文将讲述最新的用法和旧版本中的用法以及一些问题。[原文](http://t.cn/Ai9MK5r1)

<!--more-->

## TypeScript 3.0 以上

TypeScript 特意[添加了对 defaultProps 的支持](http://t.cn/Ai96R1vP)以此让类型检查符合你的预期：

```js
import React from 'reac'
import { Text } from 'react-native'

interface Props {
  foo: string;
  bar: string;
}

export default Hello extends React.Component<Props, {}> {
  public static defaultProps = {
    foo: "default"
  }

  public render() {
    const { bar, foo } = this.props
    return (
      <Text>{ bar }, { foo.toUpperCase() }</Text>
    )
  }
}
```

Hello 这个组件可以在不传递 `foo` 属性的情况下渲染、编译正常：

```js
<Hello bar="Hello" />
```

### 注意

- 尽管`foo`不是必须的，但是我们并没有把它被标记为可选的（例如 `foo?: string`）。标记为可选的意味着它可能是 `undefined`，但是实际上因为 `defaultProps` 提供了默认值，它绝对不可能变成 `undefined`。
- `defaultProps` 没有明确的类型声明。它的类型是由编译器推断的。
- 需要 `@types/react` 版本在 `16.4.11` 以上

## TypeScript 2.1 到 3.0

你可以使用 TypeScript 的 `Partial type` 特性，这意味着当前的接口只会实现被包裹的接口的一部分，这样我们可以随意拓展 `defaultProps` 而不需要改其他任何地方。

```js
import React from 'reac'
import { Text } from 'react-native'

interface Props {
  foo?: string;
  bar: number;
}

export default class MyComponent extends React.Component<Props, {}> {
  static defaultProps: Partial<Props> = {
    foo: 'default',
  }
}
```

### 注意

- 因为 TypeScript 在检查 JSX 属性时只考虑了 props，你必须把有默认值的 props 标记为可选的
- 当使用 `strictNullChecks`时，`this.props.foo` 的值可能会是 `undefined`。你可以使用非空断言（例如 `this.props.foo!`）或者类型守护（例如 `if (this.props.foo) {...}`）来移除 `undefined`。这是非常恼人的，因为实际上它是有默认值，所以绝对不会是 `undefined`，但是 TS 并不理解这个逻辑。这也是 TS 3.0 专门支持 `defaultProps` 的主要原因之一。

## 函数组件的 defaultProps

您也可以在函数组件上使用 `defaultProps`：

```js
import React from 'react'
import { Text } from 'react-native'

interface Props {
  foo: string;
  bar: number;
}

const MyComponent: React.SFC<Props> = props => (
  <Text>
    Hello, {props.foo}, {props.bar}
  </Text>
)

MyComponent.defaultProps = {
  foo: 'default',
}

export default MyComponent
```

> 注意：你不必再使用 `Partial<Props>`，因为 React.SFC 已经在 TS 2.1+ 被指定为 partial.

另一个可选的方案是解构你的 props 参数然后直接符默认值：

```js
import React from 'react'
import { Text } from 'react-native'

interface Props {
  foo: string;
  bar: number;
}

const MyComponent: React.SFC<Props> = ({ foo = 'default', bar }) => {
  return (
    <Text>
      Hello, {foo}, {bar}
    </Text>
  )
}
```

## 联系作者

|                           作者微信                           |                           知识星球                           |                           赞赏作者                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb24c7412?w=200&h=200&f=jpeg&s=17183" style="width:200px"/> | <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb26af8e1?w=200&h=200&f=png&s=39093" style="width:200px"/> | <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb338c643?w=698&h=700&f=png&s=315492" style="width:200px"/> |
