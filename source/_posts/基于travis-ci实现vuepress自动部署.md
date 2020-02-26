---
title: '基于travis-ci实现vuepress自动部署'
author: '杨俊宁'
date: '2018-09-26 22:27'
categories:
	- [工具]
- tags
	- Vue.js
---

<img src="https://i.loli.net/2018/09/26/5baba6f2be8eb.jpeg" style="zoom:25%;" />

<!--more-->

## 一、安装 vuepress

> 这里只是按照

如果你想在一个现有项目中使用 VuePress，同时想要在该项目中管理文档，则应该将 VuePress 安装为本地依赖。

```bash
# 将 VuePress 作为一个本地依赖安装
yarn add -D vuepress # 或者：npm install -D vuepress

# 新建一个 docs 文件夹
mkdir docs

# 新建一个 markdown 文件
echo "# Hello VuePress!" > docs/README.md

# 开始写作
npx vuepress dev docs
```

接着，在 `package.json` 里加一些脚本:

```json
{
  "scripts": {
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  }
}
```

然后就可以开始写作了:

```bash
$ yarn docs:dev # 或者：npm run docs:dev
```

## 二、GitHub Pages 部署

1. 在 **docs/.vuepress/config.js** 中设置正确的 **base**。

如果你打算发布到 `https://<USERNAME>.github.io/`，则可以省略这一步，因为 **base** 默认即是 "/"。

在你的项目中，创建一个如下的 deploy.sh 文件（请自行判断去掉高亮行的注释）:`https://<USERNAME>.github.io/<REPO>/`（也就是说你的仓库在 `https://github.com/<USERNAME>/<REPO>`），则将 **base** 设置为 `"/<REPO>/"`。

2. 在你的项目中，创建一个如下的 **deploy.sh** 文件（请自行判断去掉高亮行的注释）:

```bash
#!/usr/bin/env sh

# 确保脚本抛出遇到的错误
set -e

# 生成静态文件
npm run docs:build

# 进入生成的文件夹
cd docs/.vuepress/dist

# 如果是发布到自定义域名
# echo 'www.example.com' > CNAME

git init
git add -A
git commit -m 'deploy'

# 如果发布到 https://<USERNAME>.github.io
# git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master

# 如果发布到 https://<USERNAME>.github.io/<REPO>
# git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

cd -
```

## 三、集成 travis-ci

先用 github 账号登录 travis 网站，然后同步你的仓库， 然后勾选我们的项目仓库（是保存文档的仓库而不是放生成页面的仓库）

### 配置 .travis.yml

然后在你的项目文件夹新建文件 **.travis.yml**， 这个文件内容根据你的项目而定，比如我们的项目可以是这样的。

```yaml
language: node_js
sudo: required
node_js:
  - 8.11.3
branch: master
cache:
  directories:
    - node_modules
before_install:
  - export TZ='Asia/Shanghai' # 设置时区
script:
  - ./deploy.sh
```

### access_token

首先在 github 的 **setting** -> **developer setting** -> **personal access token**一栏点击**generate new token**， 这下面的选项全选，然后就会生成一个 token，复制这个 token。

进入 travis 后台，在环境变量（Environment Variables）里设置键值对，比如

```
access_token <把复制的token黏贴在这>
```

### 修改 deploy.sh

集成 travis 还需要我们修改 `deploy.sh`,

```bash
#!/usr/bin/env sh

# 确保脚本抛出遇到的错误
set -e

# 生成静态文件
npm run docs:build

# 进入生成的文件夹
cd docs/.vuepress/dist

# 如果是发布到自定义域名
# echo 'www.example.com' > CNAME

git init
git add -A
git commit -m 'deploy'

git config --local user.name "杨俊宁"
git config --local user.email "1003719811@qq.com"

# 如果发布到 https://<USERNAME>.github.io
# git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master

# 如果发布到 https://<USERNAME>.github.io/<REPO>
# git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

# 如果使用 travis 持续集成
git push -f https://${access_token}@github.com/<USERNAME>/<REPO>.git master:gh-pages

cd -
```

### 将 deploy.sh 变成可执行文件

> 在项目根目录下执行该命令

```bash
git update-index --add --chmod=+x build.sh
```

现在在试着 push，观察 travis 服务器日志是否成功。

## 四、参考链接

- [搭建持续集成、基于 vuepress 的 Github Page - 知乎](https://zhuanlan.zhihu.com/p/36390666)