---
title: React Native 架构面面观
date: 2020-02-23 17:26:09
categories:
  - [移动开发, React Native]
tags:
  - 架构
  - 最佳实践
---

<img src="https://i.loli.net/2020/02/23/sZdk3vMyxOXTb9e.png" alt="image.png" style="zoom:36%;" />

<!--more-->

## TypeScript

- [@sigmayun/react-native-template-typescript](https://github.com/sigmayun/react-native-template-typescript)：本项目基于 [react-native-community/react-native-template-typescript](https://github.com/react-native-community/react-native-template-typescript) 二次开发的模板脚手架。本文的架构方案全部融进了该模板。

## 编码规范

- [@sishuguojixuefu/eslint-config](https://github.com/sishuguojixuefu/eslint-config)：支持 vue、jsx、js、ts、tsx、html 的格式化，基于 airbnb
- husky + lint-staged

> 该部分示例代码：https://bre.is/7tTKBTDM

## 网络编程

- [axios](https://www.kancloud.cn/yunye/axios/234845): Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。
- [qs](https://www.npmjs.com/package/qs): A querystring parsing and stringifying library with some added security.
- [clean-deep](https://www.npmjs.com/package/clean-deep): 从对象中递归删除空对象、空数组、空字符串、`null`和 `value`值。不改变原始数据。

> 该部分示例代码：https://bre.is/39khhJtn

## 路由管理

- React Nativegation: 这个模块是官方推荐的导航组件，升级到5之后不仅支持了 `hooks`，更是支持了动态路由。

> 该部分示例代码：https://bre.is/pzA7JArY
>
> 相关博客：https://bre.is/pzA7JArY

## 状态管理

- `mobx` + `mobx-react`: 相对于 redux 来说更简单易用，还在犹豫的同学，可以读一下有赞的一篇文章 [我为什么从Redux迁移到了Mobx](https://tech.youzan.com/mobx_vs_redux/)

## 本地存储

> react-native-simple-storage
>
> rxdb

## 启动屏

> react-native-splash-screen

## 热更新

- [react-native-pushy](https://update.reactnative.cn/home): ReactNative中文网推出的代码热更新服务，免费，但是限制多，不推荐使用
- [react-native-code-push](https://github.com/microsoft/react-native-code-push) + [AppCenter](https://appcenter.ms/)：完全免费，国内速度可能慢，适合个人开发者
- [react-native-code-push](https://github.com/microsoft/react-native-code-push) + [code-push-server](https://github.com/lisong/code-push-server)：适合公司自建热更新服务器

## UI框架

- [@ant-design/react-native](https://rn.mobile.ant.design/index-cn):  Ant Design 出品的一个基于 React Native 的 UI 组件库

## 安卓必知必会

## ios必知必会

## React Hooks

## VsCode Snippets

- React Native TypeScript Snippets
- ES7 React/Redux/GraphQL/React-Native Snippets