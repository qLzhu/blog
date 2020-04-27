---
title: Mac 卸载 App 残留、Node 和 NPM
abbrlink: 21969
date: 2020-04-17 15:30:44
categories: mac
tags:
  - node
  - npm
  - mdfind
---

Mac上我们不借助第三方应用删除App的话，通常是直接移除到垃圾篓，或者按住`⌥option`删除<span class="text-gray">（App Store 下载的 App）</span>，时间长了系统会帮我们自动清理一些App残留。但是有些文件系统是不删除的，这时我们可以借助`mdfind`命令进行查询下跟App有关的文件，例如我们删除CleanMyMac

{% note %}
mdfind 其实就是 Mac 的 Spotlight。如果你的 Spotlight 的工作不正确，也可以 `mdutil -E` 强制重建索引数据库
{% endnote %}

```bash
mdfind -name CleanMyMac

# /Applications/CleanMyMac X.app
# /Library/LaunchDaemons/com.macpaw.zh.CleanMyMac4.Agent.plist
# /Library/LaunchDaemons/com.macpaw.CleanMyMac4.Agent.plist
# ...

# 命令删除时注意要转义下空格
rm -rf /Applications/CleanMyMac\ X.app
```

还可以使用 -onlyin 参数，指定搜索的目录。例如：`mdfind -onlyin ~/downloads CleanMyMac`
<!-- more -->

## Uninstall Node PKG

卸载从官网下载的安装，使用如下命令。如果未正常卸载的使用`which`命令查看下，安装的位置是不是自己更改了

```bash
sudo rm -rf /usr/local/{bin/{node,npm},lib/node_modules/npm,lib/node,share/man/*/node.*}
```

## Install nvm

这里再说下 [Node](https://nodejs.org/en/) 版本管理的问题！当你开发项目时，该项目对 Node 的版本有要求，且跟你安装的版本不同（过高和者低了），这时你怎么办？卸载当前版本再安装项目所需的版本？如果多个项目每个项目要求的版本都不同那，总不能每个项目都是卸载再安装吧，那多麻烦！所以我们可以借助 [nvm](https://github.com/nvm-sh/nvm) 管理 Node。通过 [HomeBrew](https://brew.sh/index_zh-cn) 安装 nvm，然后再通过 nvm 安装 Node，这样一级管理一级，方便卸载和安装

```bash
# 已安装HomeBrew的请跳过
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

brew install nvm
```

安装完成后还不能直接使用，因为通过 HomeBrew 安装的 nvm，[缺少 nvm.sh](https://stackoverflow.com/questions/27651892/homebrew-installs-nvm-but-nvm-cant-be-found-afterwards) 执行脚本文件，执行时总会报 `command not found: nvm` 错误，所以需要在 `~/.bash_profile`、`~/.bashrc` 或 `~/.zshrc` 添加如下内容

```bash
export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh
```

配置完成后，`source`命令重启下相应的配置文件

## Install Node

上述步骤都设置好后，我们就可以使用nvm安装Node了
```bash
nvm install stable       #安装最新的稳定版本
nvm alias default stable #将此版本设置为默认版本
```

## nvm basic commands

```bash
nvm --help                  显示命令行帮助信息
nvm --version               打印输出已安装的nvm版本号

nvm ls-remote               列出远程仓库内所有可安装的版本
nvm install <version>       安装指定的版本
nvm reinstall-packages      重新安装当前版本的Node
nvm ls                      列出所有安装的Node版本
nvm uninstall <version>     卸载指定的版本

nvm current                 显示当前的Node版本
nvm use <version>           切换指定版本

nvm alias <name> <version>  给不同的版本号添加别名
nvm unalias <name>          删除已定义的别名
nvm alias default <version> 设置默认 node 版本
# nvm 默认是不能删除被设定为 default 版本的 node
# 特别是只安装了一个 node 的时候，这个时候我们需要先解除当前版本绑定，然后再使用 nvm uninstall 删除
nvm deactivate              解除当前版本绑定

nvm cache dir               显示nvm的缓存目录
nvm cache clear             清楚nvm的缓存目录

# 阅读拓展
# https://github.com/nvm-sh/nvm
# http://www.srcmini.com/1433.html
```
