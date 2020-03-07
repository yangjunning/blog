---
title: 大前端之Mac开发环境
date: 2020-03-05 01:06:42
categories:
  - [大前端]
tags:
  - Mac
  - 开发环境
---


![](https://i.loli.net/2020/03/07/dPCm6qwpsZn8k1o.png)

<!--more-->

## Homebrew

访问 [brew.sh](https://brew.sh/index_zh-cn) 安装。使用 Homebrew 安装 Apple（或您的 Linux 系统）没有预装但 [你需要的东西](https://formulae.brew.sh/formula/)。Homebrew

将大大降低维护环境的时间。本文后面将尽可能使用HomeBrew。

- `brew install` : 安装
- `brew uninstall`:  卸载
- `brew update`: 更新 homebrew
- `brew upgrade` : 更新已安装软件
- `brew cleanup`: 清理
- 一键清理: `brew update && brew upgrade && brew cleanup`

## Git

### 命令行工具

```sh
// Re-installing Git on Mac OSX with Brew
$ alias git='/usr/local/bin/git'
$ brew install git
```

### 可视化工具

- [GitHub Desktop](https://desktop.github.com/): GitHub Desktop简化了您的开发工作流程
- [Sourcetree](https://www.sourcetreeapp.com/): Simplicity and power in a beautiful Git GUI

### 命令行配置

1、初始化设置

```bash
$ git config --global user.name 'your_name'
$ git config --global user.email 'your_email@aliyun.com'
```

2、提高命令输出的可读性

将 `color.ui` 设置为 `auto` 可以让命令的输出拥有更高的可读性。

```bash
$ git config --global color.ui auto
```

3、git 记住用户名和密码

```bash
$ git config --global credential.helper store
```

4、git 文件名区分大小写

```bash
$ git config --global core.ignorecase false
```

5、core.autocrlf

Linux或Mac系统使用LF作为行结束符，因此你不想 Git 在签出文件时进行自动的转换；当一个以 `CRLF` 为行结束符的文件不小心被引入时你肯定想进行修正，把 `core.autocrlf` 设置成 `input` 来告诉 Git 在提交时把 `CRLF` 转换成 `LF`，签出时不转换：

```sh
$ git config --global core.autocrlf input
```

这样会在 Windows 系统上的签出文件中保留 `CRLF`，会在 Mac 和 Linux 系统上，包括仓库中保留 `LF`。

### 学习资料

- [官方Book](https://git-scm.com/book/zh/v2)
- [git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)：助你入门 git 的简明指南，木有高深内容
- [廖雪峰的git教程](http://t.cn/RK0tLXB)
- [猴子都能懂的GIT入门](https://backlog.com/git-tutorial/cn/)

## Node

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时。我们使用 `n` 模块来维护 Node 的版本：

```shell
$ brew install n
# 安装最新版
$ sudo n latest
## 其他命令##
# 安装指定版本
$ sudo n 10.16.0
# 安装最新的稳定版
$ sudo n lts
# 删除指定版本
$ n rm 12.10.0
# 除去当前版本以外的所有缓存版本
$ n prune
```

### 更新npm

```shell
$ sudo npm install -g npm
```

### nrm 管理 registry

```shell
$ sudo i -g nrm
# 列出可用的代理
$ nrm ls
# 添加私有代理
$ nrm add sigma http://192.168.2.116:4873 http://192.168.2.116:4873
# 切换代理到 sigma 源
$ nrm use sigma
```

## React Native

强烈建议一步一步按照官方的 [搭建开发环境](https://reactnative.cn/docs/getting-started.html) 进行配置。

## Navicat Premium

Navicat Premium 是一套数据库开发工具，让你从单一应用程序中同时连接 MySQL、MariaDB、MongoDB、SQL Server、Oracle、PostgreSQL 和 SQLite 数据库。它与 Amazon RDS、Amazon Aurora、Amazon Redshift、Microsoft Azure、Oracle Cloud、MongoDB Atlas、阿里云、腾讯云和华为云等云数据库兼容。你可以快速轻松地创建、管理和维护数据库。

> Mac破解版: 链接:https://pan.baidu.com/s/1SlL1_bd4qirMnF0sLwRLhA  密码:4jq6

## natapp

开启您的内网穿透之旅,调试微信的利器，请至[官网](https://natapp.cn/)下载

## IDE

- [VSCode](https://code.visualstudio.com/): Visual Studio Code是一个由微软开发，同时支持Windows 、 Linux和macOS等操作系统且开放源代码的代码编辑器，它支持测试，并内置了Git 版本控制功能，同时也具有开发环境功能，例如代码补全、代码片段和代码重构等。

  > 为了备份和分享IDE的配置，无论是 Atom 还是 VSCode 都有相应的插件，在 VSCode 中这个插件是 [Settings Sync](https://bre.is/Ar7QVWF8)。该插件是通过 GitHub Gist 来托管配置文件。我的 Gist Id: `248fa0aed5c2c89fc342599a1cceb423`

## 设置 SSH Key

在用户主目录下，看看有没有 `.ssh` 目录，如果有，再看看这个目录下有没有 `id_rsa` 和 `id_rsa.pub` 这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开 Shell（Windows下打开Git Bash），创建 SSH Key：

```bash
$ ssh-keygen -t rsa -C "young_email@aliyun.com"
```
