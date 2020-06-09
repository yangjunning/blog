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

## 联系作者

> 本文首发于个人博客：https://youngjuning.js.org/

|                           作者微信                           |                           知识星球                           |                           赞赏作者                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb24c7412?w=200&h=200&f=jpeg&s=17183" style="width:200px"/> | <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb26af8e1?w=200&h=200&f=png&s=39093" style="width:200px"/> | <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb338c643?w=698&h=700&f=png&s=315492" style="width:200px"/> |