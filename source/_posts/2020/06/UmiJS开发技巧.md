---
title: UmiJS开发技巧
date: 2020-06-08 10:17:55
categories:
 - [前端开发,React]
tags:
 - UmiJS
---

<!--more-->

## 本地开发 umi dev 时关闭 mock

> - [希望本地开发 umi dev 时可以关闭 mock](https://github.com/umijs/umi/issues/486)

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

## 联系作者

> 本文首发于个人博客：https://youngjuning.js.org/

|                           作者微信                           |                           知识星球                           |                           赞赏作者                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="https://i.loli.net/2020/02/22/q2tLiGYvhIxm3Fl.jpg" width="200px"/> | <img src="https://i.loli.net/2020/02/22/AQzLmDPopb1ufsG.png" width="200px"/> | <img src="https://i.loli.net/2020/02/23/q56X1eYZuITQpsj.png" width="200px"/> |
