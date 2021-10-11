---
title: NVM
abbrlink: 20614
date: 2021-10-07 18:02:24
categories: nvm
tags: [nvm, node, n]
---

目前大多数情况下，我们会使用 [NVM](https://github.com/nvm-sh/nvm) 或者 [N](https://github.com/tj/n) 管理工具，管理 [NodeJS](https://nodejs.org/en/download/) 版本！
注意区分 NVM 和 NPM 的区别，NVM 是管理 NodeJS 管理器，NPM 是管理 NodeJS Modules 的管理器

即：
NPM -> NodeJS -> NPM

实例场景：

{% note %}
我们正在开发5个项目，每个项目的需求都不相同，不同项目依赖不同的 NodeJS 版本，如果没有合适的管理器，那么这个问题是很棘手的，在本地安装 5 个版本的 Node 那也是不现实的
{% endnote %}
<!-- more -->

## 安装 NVM

MacOS 下通常使用 [Homebrew](https://brew.sh/index_zh-cn) 进行安装，使用 [Homebrew](https://brew.sh/index_zh-cn) 安装主要是为了方便日后的管理

1. 安装 Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

brew install nvm
```

2. 通过 Homebrew 安装的 NVM 通常会缺失 nvm.sh 执行脚本文件，所以我们需要在当前系统下的环境配置文件内追加下述语句

```bash ~/.bash_profile
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

3. 重启环境配置文件

```bash
source ~/.bash_profile
```

## 安装 NodeJS

`stable` 命令可以使 NVM 安装当前最新的 LTS 版本

```bash
nvm install stable
```

也可以安装指定的版本

```bash
nvm install v12.19.1
```

如果你不知道 NodeJS 有哪些版本，我们可以使用 `ls-remote` 命令进行查看

```bash
nvm ls-remote

# v15.13.0
# v15.14.0
# v16.0.0
# v16.1.0
# v16.2.0
# v16.3.0
# v16.4.0
# v16.4.1
# v16.4.2
# v16.5.0
# v16.6.0
# v16.6.1
# v16.6.2
# v16.7.0
# v16.8.0
# v16.9.0
# v16.9.1
# v16.10.0
```

我们还可以使用 `--lts` 参数筛选出稳定版本

```bash
nvm ls-remote --lts

# v14.15.2   (LTS: Fermium)
# v14.15.3   (LTS: Fermium)
# v14.15.4   (LTS: Fermium)
# v14.15.5   (LTS: Fermium)
# v14.16.0   (LTS: Fermium)
# v14.16.1   (LTS: Fermium)
# v14.17.0   (LTS: Fermium)
# v14.17.1   (LTS: Fermium)
# v14.17.2   (LTS: Fermium)
# v14.17.3   (LTS: Fermium)
# v14.17.4   (LTS: Fermium)
# v14.17.5   (LTS: Fermium)
# v14.17.6   (LTS: Fermium)
# v14.18.0   (Latest LTS: Fermium
```

NodeJS 安装完成后，我们需要通过 `nvm alias default stable` 命令将此版本设置为默认的版本

## NVM 命令

### help

```bash
nvm --help     #显示命令行帮助信息
nvm --version  #打印输出已安装的 NVM 版本号
```

### which

通过该命令可以查看 NodeJS 的安装位置

```bash
which node
# /Users/xx/.nvm/versions/node/v15.12.0/bin/node
```

### nvm current

查看当前使用的 NodeJS 版本

```bash
nvm current
# v15.12.0
```

### nvm ls

查看当前安装的 NodeJS 都有哪些

```bash
nvm ls

#       v15.12.0
# default -> stable (-> v15.12.0)
# node -> stable (-> v15.12.0) (default)
# stable -> 15.12 (-> v15.12.0) (default)
# iojs -> N/A (default)
# unstable -> N/A (default)
# lts/* -> lts/fermium (-> N/A)
# lts/argon -> v4.9.1 (-> N/A)
# lts/boron -> v6.17.1 (-> N/A)
# lts/carbon -> v8.17.0 (-> N/A)
# lts/dubnium -> v10.24.1 (-> N/A)
# lts/erbium -> v12.22.6 (-> N/A)
# lts/fermium -> v14.18.0 (-> N/A
```

### nvm use

切换 NodeJS 版本

```bash
nvm use v12.19.1
nvm use v12.19.1 ./index.js
```

### nvm install

安装 NodeJS 指定版本及其重新安装

```bash
nvm install v12.19.1
nvm reinstall-packages     #重新安装当前的版本
```

### nvm uninstall

卸载 NodeJS

```bash
nvm uninstall v12.19.1
```

注意：如果本地安装的 NodeJS 只有一个版本且使用过 `nvm alias default stable` 命令指定过，那么是卸载不了的，需要先解除当前版本的绑定

```bash
nvm deactivate
nvm uninstall v12.19.1
```

### nvm alias

我们可以使用 命令给不同版本的 NodeJS 定义别名，方便日后的使用

```bash
nvm alias <name> <version>
nvm unalias <name>         #删除已定义的别名
```

### nvm cache

```bash
nvm cache dir              #显示 NVM 的缓存目录
nvm cache clear            #清除 NVM 的缓存目录
```

## 拓展阅读

- [NVM](https://github.com/nvm-sh/nvm)