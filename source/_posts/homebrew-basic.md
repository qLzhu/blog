---
title: HomeBrew
abbrlink: 42669
date: 2020-04-18 18:22:38
categories: HomeBrew
tags: HomeBrew
---

[Homebrew](https://brew.sh/index_zh-cn) 是 Mac 上一款比较出色的软件包管理器，BSD 开源由 Max Howell 开发。类似 Linux 系统的 apt-get、yum。简单来说就是用来管理你软件的！

打开终端执行下述命令，安装Homebrew
```bash
sudo /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

概念词汇

- formula 安装包的描述文件
- cellar  程序安装所在的目录
- keg     具体某个包所在的目录，cellar 下的子目录
- bottle  预先编译好的包，不需要现场下载编译源码，速度会快很多；官方库中的包大多都是通过 bottle 方式安装
- tag     下载源，可以类比于 Linux 下的包管理器 repository
- cask    安装 macOS 应用的扩展，你也可以理解为有图形化界面的应用
- bundle  描述 Homebrew 依赖的扩展
<!-- more -->

相关目录

- /usr/local/Homebrew/：程序自身安装目录
- /usr/local/Homebrew/Library/Homebrew/：命令集所在目录
- /usr/local/Cellar/：程序安装软件包的路径

## 更改源

安装完成后的第一件事，应该是更改 Homebrew 源，如果没更改的话在国内的使用体验是很差的。这是国内特有的网络状况造成的，同时也给开源项目的分发造成了一定的阻碍。在这种情况下为了我们更好的使用体验，国内的一些组织或者个人就自己新建了很多镜像，例如：

{% note %}
阿里云：https://mirrors.aliyun.com/homebrew/
中科大：https://mirrors.ustc.edu.cn/
清华源：https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/
{% endnote %}

当然你能科学上网的话，也可以使用 Homebrew 官方的源（即默认的）。如果你对如何搭建科学上网的工具有兴趣的话，也可参考之前我记录的 {% post_link vpn-shadowsocks-outline %}

```bash
# 更改 Homebrew 的源，需要先进到本地 Homebrew 的仓库内
cd "$(brew --repo)"
# 更改为中科大的源
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

# Homebrew Core
cd "$(brew --repo homebrew/core)"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

# Homebrew Cask，未安装 Cask 的不用此操作
cd "$(brew --repo homebrew/cask)"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git


# Homebrew 默认的源分别是
#https://github.com/Homebrew/brew.git
#https://github.com/Homebrew/homebrew-core
#https://github.com/Homebrew/homebrew-cask

# 获取当前源的命令
git remote get-url origin
```

更换 Homebrew Bottles 源

```bash
if [$SHELL = "/bin/bash"] #bash
then
  echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/' >> ~/.bash_profile
  source ~/.bash_profile
elif [$SHELL = "/bin/zsh"] #zsh
then
  echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/' >> ~/.zshrc
  source ~/.zshrc
fi
```

## 命令

使用基础

```bash
brew help [COMMAND]                      #帮助
man brew
brew commands                            #列出所有指令
brew update                              #更细自身
brew --version                           #列出当前版本
brew config                              #列出当前配置
brew doctor                              #诊断问题并给出解决方案
```

卸载及安装
brew 在安装程序时会先更新自身，可在环境配置文件底部加入 `export HOMEBREW_NO_AUTO_UPDATE=true`
以此禁止自动更新

```bash
brew search [FORMULA]                    #搜索要安装的程序
brew install [FORMULA]                   #安装程序
brew install --verbose --debug [FORMULA] #失败时使用这条指令重新安装，查看错误信息
brew reinstall [FORMULA]                 #重新安装
brew uninstall [FORMULA]                 #卸载
brew uninstall --force [FORMULA]         #强制卸载
brew uninstall -f [FORMULA]
brew uninstall --ignore-dependencies [FORMULA]
brew rmtree -f [FORMULA]                 #卸载时连依赖的包一块卸载
```

查看程序

```bash
brew list                                #列出所有已安装的程序
brew list --version
brew deps --tree --installed             #列出所有程序的依赖
brew deps [FORMULA]                      #列出程序依赖的软件包
brew deps --tree [FORMULA]
brew info [FORMULA]                      #列出指定程序的详细信息
brew home [FORMULA]                      #访问程序的官网
```

更新

```bash
brew outdated                            #列出所有可更新的程序
brew upgrade                             #更新所有可更新的程序
brew upgrade [FORMULA]                   #更新指定程序

brew pin [FORMULA]                       #锁定（禁止更新某个程序）
brew unpin [FORMULA]                     #取消锁定
```

清除

```bash
brew --cache                             #查看缓存路径
brew cleanup -n                          #列出可清理的旧程序版本
brew cleanup                             #清除所有可清理的旧版本
brew cleanup [FORMULA]                   #清理指定程序的旧版本
```

仓库
可以理解为应用商城 https://github.com/Homebrew/brew/blob/master/docs/Taps.md

```bash
brew tap                                 #列出已配置的仓库
brew tap [user/repo] [URL]               #添加仓库
brew tap homebrew/cask-fonts
brew untap user/repo                     #卸载指定的仓库
```

服务
诸如 Nginx、MySQL 等软件，都是有一些服务端软件在后台运行，如果你希望对这些软件进行管理，可以使用 brew services 命令来进行管理

```bash
brew services list                      #列出所有服务
brew services run [FORMULA]             #运行指定服务
brew services start [FORMULA]
brew services stop [FORMULA]
brew services restart [FORMULA]
```

备份

```bash
brew bundle dump                        #备份安装的所有程序（换电脑时使用）
brew bundle                             #根据备份时生产的Brewfile文件安装所有程序
```

## FQA

### Homebrew cask

安装 Homebrew 时会自动安装 cask，cask 安装 app 的命令跟 Homebrew 几乎相同。另外你还可以到此网站 http://macappstore.org/ 查找你要安装 app

### Error: Permission denied 无权限

更新时报此错误，说明是权限不够的意思，但是 Homebrew 又不允许在命令前添加 sudo，可使用以下方式解决

```bash
sudo chown -R $(whoami) $(brew --prefix)/*
```

安装时也报权限不够的错误时，请再执行下述命令

```bash
brew install <name>
brew link <name>
```

## 拓展阅读

- [关于 tap（仓库）](https://urz.one/cn/2020-06-11/homebrew/10039)
