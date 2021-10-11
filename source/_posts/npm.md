---
title: NPM
abbrlink: 27424
date: 2020-04-18 18:37:27
categories: npm
tags: npm
---

[npm](https://docs.npmjs.com/about-npm) 是 JavaScript 世界的包管理工具，且是 [Node.JS](https://nodejs.org/en/download/) （npm 在 Node.JS 0.6 之后的版本，随着 Node.JS 一起发布）平台的默认包管理器，也是世界上最大的软件注册表，里面包含了 100w+ 个包，能够使您轻松跟踪依赖项和版本。

npm 由三部分组成：
- [网站](https://www.npmjs.com/)
- CLI 命令行工具
- registry 注册表

npm 网站也就是 npmjs.com 可以视为一个物流集散中心，该中心从开发者那里接收包，并将这些包分发给使用者。为了效率的提升，物流集散中心雇佣了很多 CLI，分配给每个用户作为私人助理，根据使用者 dependencies 的要求对快递进行管理。
<!-- more -->

## package 和 package-lock

我们开发者并不会读取 package-lock.json，通常由 `npm install` 命令生成，由 CLI 工具读取，以确保使用 npm 命令复制项目的构建环境。

###  package.json

每个 JavaScript 都可以视为 npm 的软件包，并且通过 package.json 描述项目或包的信息，简单理解可以视为我们平时发快递时快递单上的信息。当我们使用 `npm init` 初始化 JavaScript 项目时，npm 将自动在此目录下创建一个 package.json 文件，文件的内容由开发者提供：

- name：JavaScript 项目或库的名称，通过 `require()` 引用
- author：固定格式：作者 <邮箱>
- maintainers：维护者数组：作者 <邮箱> (网址)
- contributors：贡献者数组：作者 <邮箱> (网址)
- main：入口文件，当 `require()` 引用该模块时该文件会被执行，通常默认的是 index.js 文件
- version：项目版本
- keywords：关键字数组，通常用于搜索
- description：项目描述
- license：项目许可证
- homepage：项目网址
- repository：项目仓库地址
- private：true || false，设置该包是否是私有

author、version、license 可通过 `npm config set` 命令对其提前设置，下次 `init` 时系统会自动为我们填充相关信息

```bash
npm config set init.author.email "qinlzhu@outlook.com"
npm config set init.author.name "qinlzhu"
npm config set init.author.url "https://github.com/qLzhu"
npm config set init.license "MIT"
npm config set init.version "1.0.0"
```

1. dependencies
项目运行所依赖的模块（键值对），执行 `npm install` 时安装的包会注册到此属性下

2. devDependencies
项目开发所需要的模块（键值对），执行 `npm install --save-dev` 时安装的包会注册到此属性下

3. bin
用来指定各个内部命令对应的可执行文件的位置

```bash ./bin/cli.js
# 注意脚本文件的开头必须按照下述内容指定解释器
# 
# 
# !usr/bin/env node
{
  "bin": {
    "cli": "./bin/cli.js"
  }
}
```

案例解读：
上面的 cli 命令对应的可执行文件在当前目录下的 bin 内，npm 会自动寻找到该文件并在 node_modules/.bin/ 下创建该命令的符号链接（node_modules/.bin/cli）。node_modules/.bin/ 目录会在运行时加入系统的 PATH 变量，因此在运行 npm 时，就可以不带路径，直接通过命令来调用这些脚本

```bash
"scripts": {
  "build": {"./node_modules/bin/cli.js"}
}

# 简写为
"scripts": {
  "build": {"cli"}
}

# 所有 node_modules/.bin/ 目录下的命令，都可以用 npm run [command] 的格式运行
```

4. scripts
定义代码块的地方，当我在终端执行 `npm run build` 时 cli 命令会被执行，单纯的只执行 `npm run` 不加任何参数，系统会列出 scripts 下所有可执行的命令

5. bugs
设置使用者提交 bug 的网址及其邮箱

```bash
{
  "url" : "https://github.com/owner/project/issues",
  "email" : "qinlzhu@outlook.com"
}
```

6. engines
指定项目运行的 Node.JS 版本范围

```bash
{
  "engines": {
    "node" : ">=0.10.3 <0.12",
    "npm" : "~1.0.20"
  }
}
```

package.json 更多属性请查阅 [npm package-json](https://docs.npmjs.com/cli/v7/configuring-npm/package-json)


### package-lock.json

package-lock.json 描述了项目中使用的各种依赖项的确切版本，大家可以理解为某个食品的具体配方表。

## 安装npm

1. 通过 [Node.JS](https://nodejs.org/en/download/) 官网直接下载 LTS 稳定版本的 PKG 包安装
2. 通过 nvm 管理 [Node.JS](https://nodejs.org/en/download/) 安装，适用于复杂的项目

nvm 安装 Node.JS 之前需要先安装 [HomeBrew](https://brew.sh/index_zh-cn)

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

brew install nvm
```

通过 HomeBrew 安装的 nvm，缺少 nvm.sh 执行脚本文件，还需要配置 `~/.bash_profile`、`~/.bashrc` 或 `~/.zshrc` 开发环境

```bash
export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh

# MacOS Big Sur 版本请添加
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

配置完成后，`source` 命令重启下相应的环境配置文件

```bash
source ~/.bash_profile

nvm install stable       # 安装最新的 Node.JS 版本
nvm alias default stable # 将此版本设置为默认版本
```

## npm帮助

```bash
npm help npm
```

## 查看npm版本

```bash
npm -v
npm --version
```

## 更新npm版本

npm 版本要比 Node.JS 的更新的要快，有时我们安装的不一定是最新版本，大家可通过下述命令单独更新 npm

```bash
npm install npm@latest -g
```

由于国内政策可能存在网速过慢问题，推荐大家使用[淘宝的 cnpm](https://npm.taobao.org/mirrors/node)进行解决

```bash
# 临时使用
npm install express --registry https://registry.npm.taobao.org

# 全局使用
npm config set registry https://registry.npm.taobao.org
npm config get registry

# 使用 cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 更改npm安装目录

npm 默认安装在 /usr/local 目录下，通过命令可获悉安装位置

```bash
npm config get prefix #/usr/local
```

如果我们想更改它的安装位置，可以使用 `npm config set prefix` 命令对其修改

```bash
# LOCAL_PATH_URL 要更改的目录位置
npm config set prefix=$HOME/LOCAL_PATH_URL
```

位置更改完成后，我们需要重新安装 npm，并且需要更改当前的系统环境变量，即系统下的 `.profile`、`.bash_profile` 或 `.bashrc` 文件

```bash
npm install npm -g

# 使用 VIM 编辑器在 .bashrc 文件最后追加
# export PATH="$HOME/LOCAL_PATH_URL/bin:$PATH
# 英文状态下输入 :wq 保存退出
vim ~/.bashrc

# 重启环境变量文件
source ~/.bashrc
```

## npm命令

### npm init

使用 NPM 安装包之前，需要先将本地目录 `init`，会自动创建下述文件及其文件夹：

```bash
npm init
npm init -y
npm init --yes
```

- node_modules：保存包的目录
- package.json：项目描述文件
- package-lock.json：包依赖关系详细文件

### npm search

`npm search` 在 npm 资源库搜索别人的包

```bash
npm search Loadsh

# NAME                   | DESCRIPTION         | AUTHOR          | DATE       | VERSION  | KEYWORDS
# loadsh                 | Lodash modular…     | =atool          | 2019-03-12 | 0.0.4    | modules stdlib util
# loadsh-get             | 访问嵌套对象         | =allen(lisong)  | 2020-02-01 | 1.0.1    | lodash get
# eskom-loadshedding-api | Exposing the Eskom… | =polarizedions  | 2020-01-09 | 0.1.4    | eskom loadshedding api
# madoka                 | A json generator    | =allenice       | 2016-02-17 | 1.1.2    | json generator faker ohana loadsh
# e-loadsh               | 前端常用函数工具库    | =alexjia        | 2019-09-30 | 1.0.3    | javascript util
```

### npm install

标准格式命令

```bash
npm install Lodash
```

Lodash 包会安装到本地目录下的 node_modules 内，使用该插件时只需要通过 `require('Lodash')` 方式引用

```bash
const bt = require('Lodash');
```

安装 package.json 内配置好的模块

```bash
npm install
```

`npm install` 命令其它参数
```bash
# 默认自带 --save-prod 参数
# 放在 ./node_modules 下，当前目录没有则新建
npm i Lodash
npm install Lodash
npm install Lodash --save
npm install Lodash --save-prod

# 测试或开发环境下安装依赖的包
# 保存在 package.json 的 devDependencies 下
npm install Lodash --save-dev

# 安装指定包的版本
npm install Lodash@4.17.00
npm install Lodash@latest

# 全局安装
# 下载的 Loadsh 放在 /usr/local 下或者你 Node.JS 的安装目录
npm i Loadsh -g
npm install Lodash --global
```

### npm uninstall

```bash
# 删除 node_modules 目录内的所有包
npm uninstall

# 删除 package.json dependencies 选项下的包必须使用该命令
npm uninstall --save

# package.json devDependencies
npm uninstall --save-dev
```

### npm shrinkwrap

锁定 dependencies 项目运行所依赖的模块，主要目的是为了防止用户随意更改包的依赖造成不必要的麻烦

```bash
# 执行该命令，系统会在目录下创建一个 npm-shrinkwrap.json 文件
# 当用户安装 dependencies 下不存在的包时会报错
npm shrinkwrap
```

### npm outdated

检测 node_modules 目录内的包是否有可更新版本

```bash
npm outdated
```

指定递归层级

```bash
npm outdated -g --depth=0
```

### npm update

npm 指定包更新到最新版本

```bash
npm update Loadsh
```

全部升级到最新版本

```bash
npm update
```

### npm list

枚列当前安装的所有包的版本

```bash
npm ls
npm list
npm list --global
# /usr/local/lib
# ├─┬ cnpm@6.1.1
# │ ├─┬ commander@2.10.0
# │ │ └── graceful-readlink@1.0.1
# │ ├─┬ cross-spawn@0.2.9
# │ │ └── lru-cache@2.7.3

# 使用 --depth=“n” 参数来指定递归的深度
npm list -g --depth=1
# ├─┬ cnpm@6.1.1
# │ ├── commander@2.10.0
# │ ├── cross-spawn@0.2.9
```

### npm audit

使用此命令检测包是否有安全问题，有的使用 `npm audit fix` 修复

### npm cache

每当你通过 npm 安装一个软件包时，npm 会自动在你系统用户根目录下创建一个副本，并保存到 `.npm` 目录内，之所以你安装相同的软件包时，速度会更快，那是因为它不需要再访问网络安装了，所以我们需要定期清理

```bash
ls ~/.npm

npm cache clean --force
```

## 发布自己包

### npm init

首先在包目录下初始化，然后根据系统提示依次输入必要的信息，最后生成 package.json。或者执行 `npm init -y` 直接生成 package.json 跳过提示

### npm adduser

注册 npm 资源库账户

```bash
npm adduser
# npm notice Log in on https://registry.npmjs.org/
# Username: qinlzhu
# Password: 
# Email: (this IS public) qinlzhu@outlook.com
```

### npm login
注册过的可使用此命令直接登陆

### npm whoami
用于检测当前使用的用户

### npm publish

编辑好包后，通过此命令发布到线上

## 常见错误

执行 `npm install` 安装插件时报下述错误！这是 npm 对 node_modules 没有读写的权限造成的，执行时须在开头添加 `sudo`。

```bash
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules
npm ERR! code EACCES
npm ERR! syscall access
npm ERR! path /usr/local/lib/node_modules
npm ERR! errno -13
npm ERR! Error: EACCES: permission denied, access '/usr/local/lib/node_modules'
npm ERR!  [Error: EACCES: permission denied, access '/usr/local/lib/node_modules'] {
npm ERR!   errno: -13,
npm ERR!   code: 'EACCES',
npm ERR!   syscall: 'access',
npm ERR!   path: '/usr/local/lib/node_modules'
npm ERR! }
npm ERR! 
npm ERR! The operation was rejected by your operating system.
npm ERR! It is likely you do not have the permissions to access this file as the current user
npm ERR! 
npm ERR! If you believe this might be a permissions issue, please double-check the
npm ERR! permissions of the file and its containing directories, or try running
npm ERR! the command again as root/Administrator.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/**/.npm/_logs/2020-11-20T05_26_26_608Z-debug.log
```

https://npm.comptechs.cn/
http://caibaojian.com/npm/
https://cloud.tencent.com/developer/doc/1282
https://chinese.freecodecamp.org/news/what-is-npm-a-node-package-manager-tutorial-for-beginners/