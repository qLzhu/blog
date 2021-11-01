---
title: python
categories: python
tags:
  - python
  - pip
abbrlink: 51872
date: 2021-10-18 19:42:12
---

Apple 在 MacOS 机器上预装的是 [python 2.7.16](https://www.python.org/) 版本，目前已废弃。现在 python 社区使用的是 python 3.x，python 4.x 也即将推出，将会完全向后兼容。如果你在终端下运行 python，将会收到不推荐使用 Python 2.7 的警告：

```bash
$: python 

WARNING: Python 2.7 is not recommended. 
This version is included in macOS for compatibility with legacy software. 
Future versions of macOS will not include Python 2.7. 
Instead, it is recommended that you transition to using 'python3' from within Terminal.

Python 2.7.16 (default, Aug 30 2021, 14:43:11) 
[GCC Apple LLVM 12.0.5 (clang-1205.0.19.59.6) [+internal-os, ptrauth-isa=deploy on darwin
Type "help", "copyright", "credits" or "license" for more information.
```
<!-- more -->

如果你想使用 python 3 及其以上版本，你就不得不去另行安装，你可以通过以下方式安装

- 官网下载安装包
- HomeBrew 直接安装
- HomeBrew + pyenv 方式安装

本实例采用第三种方案安装，这种方案主要是便于后期管理

## 工具的安装

### Xcode CommandLineTools

这是 Apple 为开发者提供的集成开发工具，里面内置了很多开发时所需的库及其语言
```bash
sudo xcode-select --install
```

如果之前安装过请删除重新安装
```bash
sudo rm -rf /Library/Developer/CommandLineTools
sudo xcode-select --install
```

### Homebrew

这里我们需要使用 Homebrew 安装 pyenv，如果你还不知道它是什么！请查阅 {% post_link homebrew-basic %}

安装
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装完成后，我们还需要 Homebrew 浅层克隆的问题
```bash
git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask fetch --unshallow
```

否则会报 No formulae found in taps 错误
```bash
Error: No previously deleted formula found.
==> Searching taps on GitHub...
Error: No formulae found in taps.
Error: 
  homebrew-core is a shallow clone.
To `brew update`, first run:
  git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core fetch --unshallow
```

## pyenv 工作原理

当我们执行 `ls` 之类的命令时，shell 解释器会从一个名为 PATH 的环境变量值中，从左向右进行搜索 ls 可执行文件。有则执行，没有则报 `command no found` 错误。PATH 值是一个目录列表，目录用冒号分隔
```bash
/usr/local/bin:/usr/bin:/bin
```

此示例：
shell 解释器会先搜索 /usr/local/bin 目录，然后再搜索 /usr/bin，然后是 /bin。所以我们只要控制 PATH 变量，就能够做到 python 版本的切换。pyenv 则是通过在 PATH 的头部插入 Shim 目录，架空后面的系统路径。以此实现对 python 版本的切换。简单理解，把 Shim 想象成一个变量，切换版本时 Shim 的值会更改就可以了

## 安装 pyenv

Homebrew 安装 python 构建环境相关依赖及其 pyenv
```bash
brew install openssl readline sqlite3 xz zlib pyenv
```

如果报 Permission denied 错误
```bash
==> Pouring readline-8.1.1.big_sur.bottle.tar.gz
cp: setattrlist: /usr/local/Cellar/readline/.: Permission denied
cp: chmod: /usr/local/Cellar/readline/.: Operation not permitted
Error: Failure while executing; `cp -pR /private/tmp/d20211025-97971-19184wv/readline/. /usr/local/Cellar/readline` 
exited with 1. Here's the output:
cp: setattrlist: /usr/local/Cellar/readline/.: Permission denied
cp: chmod: /usr/local/Cellar/readline/.: Operation not permitted
```

请执行 `sudo chown -R $(whoami) $(brew --prefix)/*` 指令后再安装，这是由于权限不够造成的

## 设置 PATH

请分别执行下述指令，这里我使用的是 [zsh](https://github.com/ohmyzsh/ohmyzsh)

- PYENV_ROOT：pyenv 目录位置

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init --path)"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
source ~/.zshrc
reset
```

## 安装 python

查看可安装的版本

```bash
pyenv install --list

#Available versions:
#  2.1.3
#  2.2.3
#  2.3.7
#  2.4.0
```

安装指定的版本

```bash
pyenv install 3.10.0
```

设置系统默认版本

```bash
pyenv global 3.10.0
pyenv versions

#system
#* 3.10.0 (set by /Users/qinlzhu/.pyenv/version)
```

也可以使用 local 设置当前目录所使用的版本

注意：
有时设置了 pyenv.local 版本后，再设置 global 会发现没有生效，可以使用 `pyenv local --unset` 解除

```bash
pyenv local 3.8.0
```

恢复系统默认的版本

```bash
pyenv global system
```

升级

```bash
cd $(pyenv root)
git pull
```

## 相关命令

```bash
pyenv commands #列出所有命令
```

如果你想让不同的项目拥有自己独立的运行环境，避免相互干扰，可以使用 [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv) 创建虚拟环境

## 拓展阅读

- [python](https://www.python.org/downloads/)
- [pyenv](https://github.com/pyenv/pyenv)
- [Suggested build environment](https://github.com/pyenv/pyenv/wiki#suggested-build-environment)
- [如何在 Mac 上安装 Python 3](https://chinese.freecodecamp.org/news/python-version-on-mac-update/)
- [Xcode CommandLineTools](https://developer.apple.com/download/more/)
- [mkdocs](https://www.mkdocs.org/getting-started/)