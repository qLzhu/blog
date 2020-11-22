---
title: NPM Basic
abbrlink: 27424
date: 2020-04-18 18:37:27
categories: npm
tags: npm
---

在使用npm之前，须先安装 [Node.js](https://nodejs.org/en/download/)，因为npm是随Node一起发布的，所以推荐安装 LTS 版的稳定版本。如果网速比较慢的话，推荐到 [TaoBao Node.js](https://npm.taobao.org/mirrors/node) 镜像下载。建议大家使用nvm进行安装，不推荐直接下载程序包进行安装，原因请查阅 {% post_link mac-uninstall-app %}

Node.js安装完成后，可通过相关命令查看安装位置及其版本，以此来检测是否安装成功

```bash
which node     #/usr/local/bin/node
node --version #v12.14.1
npm --version  #6.13.4
```
<!-- more -->

由于npm的更新频率比Node.js多，所以通过上面的命令安装的npm可能不是最新版本，可以通过下面的命令单独更新npm

```bash
npm install npm@latest -g
```

## 配置基本信息

执行 `npm init` 时会用到
```bash
npm set init.author.email "qinlzhu@outlook.com"
npm set init.author.name "qinlzhu"
npm set init.license "MIT"
```

通过 `npm config` 命令可查看npm的相关配置，也可根据自己的需求自定义配置，在不同的环境下执行不同的配置文件，配置加载优先级如下(从高到低)

1. 命令行参数、环境变量、npmrc 文件
2. npmrc 文件（项目级、用户级~/.npmrc、系统级、npm 内置级）

```bash
npm config list
# ; cli configs
# metrics-registry = "https://registry.npmjs.org/"
# scope = ""
# user-agent = "npm/6.13.4 node/v12.14.1 darwin x64"

# ; userconfig /Users/leizhu/.npmrc
# home = "https://npm.taobao.org"
# init-author-email = "qinlzhu@outlook.com"
# init-author-name = "qinlzhu"
# init-license = "MIT"
# registry = "https://registry.npmjs.org/"

# ; node bin location = /usr/local/bin/node
# ; cwd = /Users/leizhu/Downloads/Code/github/blog/example/class
# ; HOME = /Users/leizhu
# ; "npm config ls -l" to show all defaults.
```

## 配置 npm 源

我们使用默认配置从npm官网下载模块时，由于网络的因素，会导致我们的下载速度特别慢。所以我们可以配置一些国内的镜像来加快我们的下载速度。这里以淘宝的npm镜像为例

```bash
# 临时使用
npm install express --registry https://registry.npm.taobao.org

# 全局使用
npm config set registry https://registry.npm.taobao.org
npm config get registry

# 使用 cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 更改安装目录

更改目录后需要重新安装npm，然后再更改环境变量。即在 `.profile`、`.bash_profile(Mac)` 或 `.bashrc` 文件内最后一行添加 `export PATH="$HOME/Folder-Name/bin:$PATH"`，最后再重启下文件即可

```bash
# 获取全局安装位置
npm config get prefix #/usr/local

# 更改安装位置(path_url换成你要安装的位置)
npm config set prefix=$HOME/path_url

# 重新下载npm并重启配置文件
npm install npm -g
vim ~/.bashrc
source ~/.bashrc
```

## 初始化

使用npm安装软件包之前，需要先初始化本地目录。命令执行后会在工程根目录下，生成如下文件及其目录

{% note %}
node_modules：保存软件包的文件夹
package.json：软件包管理文件
package-lock.json：软件包各种依赖关系管理文件
{% endnote %}

```bash
npm init

# 回车后，它会询问一些关于包的基本信息，根据实际情况回答即可
# 如果不喜欢这种方式，可以使用 `npm init --yes` 或 `npm init -y` 命令
# 直接使用默认的配置来创建 package.json 文件
#
# package name: (npms)     项目名称
# version: (1.0.0)         版本信息
# description:             项目简单描述，如果为空，
#                          读取当前目录的 READMD.md 或 README 文件第一行内容作为它的默认值
# entry point: (index.js)  入口文件
# test command:            test脚本
# git repository:          项目地址
# keywords:                项目关键词
# license: (MIT)           项目许可协议

# package.json
#
# {
#   "name": "npms",
#   "version": "1.0.0",
#   "description": "",
#   "main": "index.js",
#   "dependencies": {
#     "lodash": "^4.17.15"
#   },
#   "devDependencies": {},
#   "scripts": {
#     "test": "echo \"Error: no test specified\" && exit 1"
#   },
#   "author": "qinlzhu <qinlzhu@outlook.com>",
#   "license": "MIT"
# }
```

## 搜索安装包

```bash
npm search Loadsh

# NAME                   | DESCRIPTION         | AUTHOR          | DATE       | VERSION  | KEYWORDS
# loadsh                 | Lodash modular…     | =atool          | 2019-03-12 | 0.0.4    | modules stdlib util
# loadsh-get             | 访问嵌套对象         | =allen(lisong)  | 2020-02-01 | 1.0.1    | lodash get
# eskom-loadshedding-api | Exposing the Eskom… | =polarizedions  | 2020-01-09 | 0.1.4    | eskom loadshedding api
# madoka                 | A json generator    | =allenice       | 2016-02-17 | 1.1.2    | json generator faker ohana loadsh
# e-loadsh               | 前端常用函数工具库    | =alexjia        | 2019-09-30 | 1.0.3    | javascript util
```

## 安装

```bash
# 命令格式
npm install <package_name>

# 本地安装
# 生产环境下安装依赖的软件包，默认自带 --save-prod 参数
npm install Lodash
# 等同于
npm i Lodash
npm install Lodash --save
npm install Lodash --save-prod

# 测试或开发环境下安装依赖的软件包
# 保存在 package.json 的 devDependencies 下
npm install Lodash --save-dev

# 安装软件包的指定版本
npm install Lodash@4.17.00
npm install Lodash@latest

# 全局安装
npm install Lodash --global
npm i Loadsh -g
```

## 更新

```bash
# 检测更新
npm outdated
npm outdated -g --depth=0

# 更新
npm update
npm update Loadsh
```

## 卸载

```bash
# 删除 node_modules 目录内的包
npm uninstall

# 删除 package.json dependencies 选项下的包必须使用该命令
npm uninstall --save

# package.json devDependencies
npm uninstall --save-dev
```

## 枚列

```bash
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

## 缓存

每当你通过npm安装一个软件包时，npm会自动在你系统用户根目录下创建一个副本，并保存到 `.npm` 目录内，之所以你安装相同的软件包时，速度会更快，那是因为它不需要再访问网络安装了，所以我们需要定期清理

```bash
ls ~/.npm

npm cache clean --force
```

## 常见错误

执行 npm install 安装插件时报下述错误！这是 npm 对 node_modules 没有读写的权限造成的，执行时只需在开头添加 sudo 命令即可。

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