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

## ohmysh

```sh
# 切换 shell 为 zsh
$ chsh -s /bin/zsh
# 通过 curl 安装 ohmysh
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### Agnoster 主题配置

- 终端执行 `open ~/.zshrc`
- 找到 ZSH_THEME 修改为: `ZSH_THEME="agnoster"`
- `source ~/.zshrc`
- 安装 Powerline 对应的字体库
   - `git clone https://github.com/powerline/fonts.git`
   - `cd fonts`
   - `install.sh`
   - `cd ..`
   - `rm -rf fonts`
   - 进入：终端 > 偏好设置 > 字体修改为 ：`Meslo LG S DZ Regular for Powerline`。
      ![](https://i.loli.net/2020/03/25/wHBrfAs2kScPjYR.png)

### 插件

#### zsh-syntax-highlighting

- 安装: `brew install zsh-syntax-highlighting`
- Oh-my-zsh
   - `git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`
   - `plugins=( [plugins...] zsh-syntax-highlighting)`
   - `source ~/.zshrc`

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

Navicat Premium 是一套数据库开发工具，让你从单一应用程序中同时连接 MySQL、MariaDB、MongoDB、SQL Server、Oracle、PostgreSQL 和 SQLite 数据库。它与 Amazon RDS、Amazon Aurora、Amazon Redshift、Microsoft Azure、Oracle Cloud、MongoDB Atlas、腾讯云和华为云等云数据库兼容。你可以快速轻松地创建、管理和维护数据库。

> Mac破解版: 链接:https://pan.baidu.com/s/1SlL1_bd4qirMnF0sLwRLhA  密码:4jq6

## MySql

```shell
$ brew install mysal
```

### 修改配置文件

```shell
$ nano /usr/local/etc/my.cnf
```

### 启动服务

```shell
brew services start mysql
# 重启
brew services restart mysql
# 停止
brew services stop mysql
```

> Or, if you don't want/need a background service you can just run: `mysql.server start`

### 安全设置

```shell
$ mysql_secure_installation
```

1. 建立密码验证插件

   ```shell
   Securing the MySQL server deployment.

   Connecting to MySQL using a blank password.

   VALIDATE PASSWORD PLUGIN can be used to test passwords and improve security. It checks the strength of password and allows the users to set only those passwords which are secure enough. Would you like to setup VALIDATE PASSWORD plugin?

   Press y|Y for Yes, any other key for No: y
   ```

2. 选择密码规则

   ```shell
   There are three levels of password validation policy:

   LOW    Length >= 8
   #长度大于等于8
   MEDIUM Length >= 8, numeric, mixed case, and special characters
   #长度大于等于8，数字、大小写字母、特殊符号
   STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file
   #长度大于等于8，数字、大小写字母、特殊符号和字典文件（慎选！）

   Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
   Please set the password for root here.

   New password: （输入你的密码）
   Re-enter new password: （再次输入你的密码）
   ```

3. 创建符合规则的新密码

   ```shell
   Estimated strength of the password: 50 		#密码强度
   Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
   ```

4. 删除匿名用户

   ```
   By default, a MySQL installation has an anonymous user, allowing anyone to log into MySQL without having to have a user account created for them. This is intended only for testing, and to make the installation go a bit smoother.
   You should remove them before moving into a production environment.

   Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
   Success.
   ```

5. 禁止远程登录

   ```shell
   Normally, root should only be allowed to connect from 'localhost'. This ensures that someone cannot guess at the root password from the network.

   Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
   Success.
   ```

6. 删除测试数据表

   ```shell
   By default, MySQL comes with a database named 'test' that anyone can access. This is also intended only for testing, and should be removed before moving into a production environment.

   Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
    - Dropping test database...
   Success.

    - Removing privileges on test database...
   Success.
   ```

7. Done

   ```shell
   Reloading the privilege tables will ensure that all changes made so far will take effect immediately.

   Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
   #是否重新加载权限表
   Success.

   All done!
   ```

#### Your password does not satisfy the current policy requirements.

如果你在选择密码规则的时候不小心选择了2，也就是数字、大小写字母、特殊符号和字典文件的组合。这时你会发现 `mysql_secure_installation`不会再给你机会重新设置了。手动微笑，mmp。方法还是有的：

```shell
SHOW VARIABLES LIKE 'validate_password%';
```

![](https://i.loli.net/2020/03/09/m2GRbrPeHDkC7pQ.png)

使用命令 `mysql -u root` 登陆，执行：

```shell
set global validate_password.policy=0;
set global validate_password.length=4;
#将密码规则设置为LOW，就可以使用纯数字纯字母密码
```

### 登陆

```shell
$ mysql -u root -p
```

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

## 参考

- [oh-my-zsh,让你的终端从未这么爽过](https://www.jianshu.com/p/d194d29e488c)

## 联系作者

|                           作者微信                           |                           知识星球                           |                           赞赏作者                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb24c7412?w=200&h=200&f=jpeg&s=17183" style="width:200px"/> | <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb26af8e1?w=200&h=200&f=png&s=39093" style="width:200px"/> | <img src="https://user-gold-cdn.xitu.io/2020/2/24/17074acbb338c643?w=698&h=700&f=png&s=315492" style="width:200px"/> |
