---
title: HomeBrew Basic
abbrlink: 42669
date: 2020-04-18 18:22:38
categories: HomeBrew
tags: HomeBrew
---

[Homebrew](https://brew.sh/index_zh-cn) 是 Mac 上一款比较出色的软件包管理器，类似 Linux 系统的 apt-get、yum。简单来说就是用来管理你软件的！

打开终端执行下述命令，安装Homebrew
```bash
sudo /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```
<!-- more -->

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

## 基本命令

- 查看帮助：`brew help`
- 查看当前版本：`brew --version`
- 更新Homebrew：`brew update`
- 搜索包：`brew search <name>`
- 安装包：`brew install <name>`
- 卸载包：`brew uninstall <name>`
- 彻底卸载包：`brew uninstall <name> --force`
- 强制卸载包：`brew uninstall --ignore-dependencies <name>`
- 查看包信息：`brew info <name>`
- 列出指定包的依赖关系：`brew deps <name>`
- 列出已安装的包：`brew list`
- 列出可更新的包：`brew outdated`
- 更新所有安装过的包：`brew upgrade`
- 锁定指定的包：`brew pin <name>`
- 取消锁定：`brew unpin <name>`
- 更新指定的包：`brew upgrade <name>`
- 查看可清理的旧版本包：`brew cleanup -n`
- 清理指定版本的旧包：`brew cleanup <name>`
- 清理所有旧版本的包：`brew cleanup`

## 常见错误

## Error: Permission denied

更新时报此错误，说明是权限不够的意思，但是 Homebrew 又不允许在命令前添加 sudo，可使用以下方式解决

```bash
sudo chown -R $(whoami) $(brew --prefix)/*
```

如果 `install` 软件包时，报的 Permission denied 错误，请逐步按照下述命令解决

```bash
sudo chown -R $(whoami) $(brew --prefix)/*
brew install <name>
brew link <name>
```