---
title: babel-plugin-root-import中文文档
categories:
  - [前端开发, Babel]
tags:
  - 翻译
mp3:
date: 2019-06-04 07:28:24
---

一个可以让你基于根路径导入组件的 Babel 插件（支持 `import`、`require`）

### 示例

```js
// 不使用这个插件...
import SomeExample from '../../../some/example.js'
const OtherExample = require('../../../other/example.js')

// 使用 babel-plugin-root-import 你可以这么写...
import SomeExample from '~/some/example.js'
const OtherExample = require('~/other/example.js')
```

### 安装

```sh
$ npm install babel-plugin-root-import -D
# or
$ yarn add babel-plugin-root-import -D
```

### 使用

添加到 babel 配置文件的 `plugins` 数组中，例如 `.babelrc`

```js
{
  "plugins": [
    ["babel-plugin-root-import"]
  ],
}
```

最近的 react-native 版本中，Babel 配置文件是`babel.config.js`：

```js
module.exports = {
  plugins: [['babel-plugin-root-import']],
}
```

### 配置

#### 默认配置

```js
module.exports = {
  plugins: [
    [
      'babel-plugin-root-import',
      {
        rootPathSuffix: './',
        rootPathPrefix: '~',
      },
    ],
  ],
}
```

#### 自定义 rootPathSuffix

> 该配置用来改变根目录。

```js
module.exports = {
  plugins: [
    [
      'babel-plugin-root-import',
      {
        rootPathSuffix: 'src',
      },
    ],
  ],
}
```

`"src"` 和 `"./src"` 是一样的。

#### 自定义 rootPathPrefix

如果你不喜欢 `~` 符号，你可以使用你自己的符号（比如 `#` 或 `\`）。因为新版的 NPM 允许`@`作为包的名字，所以不推荐使用 `@`。

> 注意：该配置项的值必须是 1 个或两个字符

```js
module.exports = {
  plugins: [
    [
      'babel-plugin-root-import',
      {
        rootPathPrefix: '#',
      },
    ],
  ],
}
// 现在你就可以像下面一样使用
import foo from '#my-file'
import foo from '#/my-file'
```

如果你设置成 `"#/"`，你就不能省略 `/`。

#### 多个自定义 prefixes 和 suffixes

```js
{
  plugins: [
    [
      'babel-plugin-root-import',
      {
        paths: [
          {
            // `~` is the default so you can remove this if you want
            rootPathPrefix: '~',
            rootPathSuffix: 'src/js',
          },
          {
            rootPathPrefix: '@',
            rootPathSuffix: 'other-src/js',
          },
          {
            rootPathPrefix: '#',
            rootPathSuffix: '../../src/in/parent',
          },
        ],
      },
    ],
  ]
}

// 现在你就可以像下面一样使用
import foo from '~/foo'
const bar = require('@/bar')
```

#### 不要让 ESLint 迷惑

如果你使用 [eslint-plugin-import](https://github.com/benmosher/eslint-plugin-import) 验证组件导入，就必须知道 ESLint 来解析根目录导入。你可以使用 [eslint-import-resolver-babel-plugin-root-import](http://t.cn/EaZKaTK)

```sh
$ yarn add eslint-plugin-import eslint-import-resolver-babel-plugin-root-import -D
```

在 ESLint 配置文件中添加如下配置：

```js
module.exports = {
  settings: {
    'import/resolver': {
      'babel-plugin-root-import': {
        rootPathPrefix: '~',
        rootPathSuffix: './',
      },
    },
  },
}
```

#### 不要让 VSCode 迷惑

在 `tsconfig.json` 中配置：

```json
{
  "compilerOptions": {
    "allowJS": true,
    "baseUrl": ".",
    "paths": {
      "~/*": ["./*"]
    }
  }
}
```