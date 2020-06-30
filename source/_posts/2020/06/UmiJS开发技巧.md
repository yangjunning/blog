---
title: UmiJS开发技巧
date: 2020-06-08 10:17:55
categories:
 - [前端开发,React]
tags:
 - UmiJS
---

![image.png](https://i.loli.net/2020/06/23/LYNpFyiax7mT1ZB.png)

<!--more-->

鉴于 UmiJS 的文档写的实在不理想，而且是重度的封装了很多工具和功能。对于刚接触的人形成了一层技术壁垒（大佬请忽略这句话）。所以我总结了使用UmiJS 开发中遇到的坑和技巧。

## 本地开发 umi dev 时关闭 mock

> 参考: [希望本地开发 umi dev 时可以关闭 mock](https://github.com/umijs/umi/issues/486)

方案一（推荐）：在 npm scripts 中加入以下指令：

```json
{
  "scripts": {
    "dev": "MOCK=none umi dev"
  }
}
```

方案二：在 `.env` 文件里 设置 `MOCK=none` 也可以关闭

## jsx无法转到定义处

在 `jsconfig.json` 文件中进行如下配置：

```json
{
  "compilerOptions": {
    "jsx": "react"
  }
}
```

## 在umi中如何访问静态资源

在umi框架中，图片等静态资源主要放到三个地方：

1. 在 `/public` 目录下，一般放共享资源。
2. 在 `/src/assets/` 目录下，一般放全局静态资源。
3. 在 `/src/pages/` 里的各个页面目录下，放在这里的好处是更符合组件化开发的思想，便于拷贝复用。

由于静态资源会受 `context.config.publicPath` 的影响，所以在 `document.ejs` 中应该这样引入比较安全：

```html
<script type="text/javascript" src="<%= context.config.publicPath %>ol.js" />
```

## 如何访问静态图片

1、如果在/public目录下的静态图片，可以直接输入绝对路径，假设/public/yay.jpg,访问方式如下：

`<img src="/yay.jpg" />`

注意：以上必须构建后在dist中才能看到。

2、在 `/src/assets` 和 `/src/pages/` 目录下的图片，不能通过输入绝对路径访问，必须先 `import` 导入，才能访问。或者 `require` 导入。比如 `/src/assets/yay.jpg` 需：

```jsx
import yayImg from '/src/assets/yay.jpg';
<img src={yayImg} />

<img src={require('/src/assets/yay.jpg')}
```

为什么会这样呢？主要是因为构建时，`/public` 目录下的文件会原样复制到 `/dist/` 目录下，而 `/src/assets/` 和 `/src/pages/` 目录下的文件会被改名并复制到 `/dist/` 下。

## react-router三种传参方式

> 参考: [react-router三种传参方式](https://www.kelede.win/posts/react-router%E4%B8%89%E7%A7%8D%E4%BC%A0%E5%8F%82%E6%96%B9%E5%BC%8F/)

```jsx
import { Component } from 'react'
import router from 'umi/router'

const RouterDemo = () => {
  const onOk = () => {
    router.push({
      pathname: 'test/router',
      // 点击之后，页面会跳转且地址上会跟上query的参数，?id=1&code=123
      // 获取方式传值内容的方式: this.props.location.query
      query: {
        id: '1',
        code: '123',
      },
      // 点击之后，页面会跳转
      // 通过this.props.location.params可以获得params的值
      // params可以为其他名字，如text、nihao等，不一定是params
      // 刷新页面后，params的值会丢失。
      params: {
        d: '1',
        code: '123',
      },
      // 使用state传值和params传值一样，都不会再url中显示出来
      // state传值与params传值的区别是state传值刷新页面值还在，而使用params传值刷新后值不没有了。
      state: {
        d: '1',
        code: '123',
      },
    })
  }
  return <div onClick={this.onOk}>点击测试</div>
}
```

## 修改浏览器上方图标

> 参考: [HTML 配置模板](https://v2.umijs.org/zh/guide/html-template.html#配置模板)

```html
<!-- 图片在 /public 下 -->
<link rel="icon" type="image/x-icon" href="<%= context.publicPath %>favicon.png" />
<!-- 图片在 /src/assets/ 下-->
<link rel="icon" type="image/x-icon" href="<%= context.publicPath %>static/favicon.png" />
```

## 支持 ie11

> 参考: [ie11兼容问题](https://github.com/umijs/umi/issues/1394)

配置浏览器最低版本，会自动引入 polyfill 和做语法转换，配置的 targets 会和合并到默认值，所以不需要重复配置:

```js
// umi默认兼容最低浏览器版本
// Default: { chrome: 49, firefox: 45, safari: 10, edge: 13, ios: 10 }
export default {
  targets: {
    ie: 11,
  },
};
```

## 编译 node_modules 下的包

### UmiJS 2.x

> 参考 [How to configure extraBabelIncludes](https://github.com/umijs/umi/issues/2117#issuecomment-539982434)

```js
const path = require('path');
{
  extraBabelIncludes: [path.resolve(__dirname, 'node_modules/<package_name>')],
}
```

### UmiJS 3.1+

> 参考: [nodeModulesTransform](https://umijs.org/zh-CN/config#nodemodulestransform-31)、[如何做编译提速](https://umijs.org/zh-CN/guide/boost-compile-speed)

UmiJS 3 删除了 `extraBabelIncludes` 和 `es5ImcompatibleVersions`，`node_modules` 也走 babel 编译后就没有意义了，无需配置

UmiJS 3 默认编译 `node_modules` 下的文件，带来一些收益的同时，也增加了额外的编译时间。如果不希望 `node_modules` 下的文件走 babel 编译，可通过以下配置减少 40% 到 60% 的编译时间。

```js
export default {
  nodeModulesTransform: {
    type: 'none',
    exclude: [], // 忽略的依赖库，包名，暂不支持绝对路径；可通过 exclude 配置添加额外需要编译的
  },
}
```

## 联系作者

> 本文首发于个人博客：https://youngjuning.js.org/

|                           作者微信                           |                           赞赏作者                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="https://i.loli.net/2020/02/22/q2tLiGYvhIxm3Fl.jpg" width="200px"/> | <img src="https://i.loli.net/2020/02/23/q56X1eYZuITQpsj.png" width="200px"/> |
