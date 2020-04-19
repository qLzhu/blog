---
title: About Terminal
abbrlink: 24678
date: 2020-04-17 21:18:34
categories: mac
tags:
  - terminal
  - bash
  - zsh
  - git
---

## 快捷键

这里只列出几个常用的，更多请查阅 "帮助 - 终端帮助 - 键盘快捷键"

- `⌘Command + N`：再打开一个终端
- `⌘Command + T`：新建窗口
- `⇧Shift + ⌘Command + K`：新建远程连接
- `⌃Control + A`：光标移到行开头
- `⌃Control + E`：光标移到行结尾
- `⌃Control + U`：光标位置删除到行开头
- `⌃Control + K`：光标位置删除到行尾
- `⌃Control + W`：删除光标位置左侧一个单词
- `⌥option + D`：删除光标位置右侧一个单词<span class="text-gray">（终端 - 偏好设置 - 描述文件 - 键盘 - 勾选将option键用作Meta键）</span>
- `⌥option + 左箭头`：光标前移一个单词
- `⌥option + 右箭头`：光标后移一个单词
- `⌘Command + .`：中断<span class="text-gray">（跳转到下一行重新开始）</span>
<!-- more -->

## 定制shell提示符

我们需要把设置添加到Shell解释器的配置文件内，才可实现定制终端化的目的！bash用户编辑`~/.bash_profile`文件；zsh编辑`~/.zshrc`文件；不知道用的是什么解释器的使用`echo $SHELL`命令进行查看

例如：在`~/.zshrc`内添加`export PS1=“\u@\h\w $ ”`，终端提示会变成下面这样
```
Admin@MacBook~Desktop/ $
```

PS1后的参数，例如：
- \W：当前工作目录名的最后部分
- \w：完整目录名
- \$：显示"$"字符，超级用户显示一个"#"字符
- \l：当前终端设备名
- \d：当前日期
- \t：当前时间
- \h：hostname

https://billie66.github.io/TLCL/book/chap14.html
http://bashrcgenerator.com/
emoji表情 https://emojipedia.org/

如果你要修改终端内的用户名的话，可使用`sudo scutil --set HostName yourname`

## 安装Command line tools

```
xcode-select –install
```

在弹出的界面点 install 同意即可<span class="text-gray">（经常使用VPN工具的，记得要关闭）</span>
{% post_link mac-xcode-select-install "关于xcode-select –install的解释" %}

## 切换shell

`cat /etc/shells`命令可以查看当前系统可以使用哪些shell
`echo $SHELL`命令可以查看我们当前正在使用的shell  

切换shell
```
chsh -s /bin/zsh
```

## tab自动补全

打开Terminal输入`nano .inputrc`，再输入下述内容

```bash
set completion-ignore-case on
set show-all-if-ambiguous on
TAB: menu-complete
```

输入后按 `⌃Control ＋ o` 键，然后关闭、重启

## 安装oh-my-zsh

```bash
git clone https://github.com/ohmyzsh/ohmyzsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

# 切换shell解释器为zsh
chsh -s $(which zsh)
```

完成后重启终端。卸载oh-my-zsh直接在终端执行 `uninstall_oh_my_zsh` 进行卸载！更改主题可到 https://github.com/ohmyzsh/ohmyzsh/wiki/Themes 进行查看，然后修改 ~/.zshrc 文件中的 `ZSH_THEME=""` 模块，再 source ~/.zshrc 重启下即可完成主题的更改

oh-my-zsh内置了很多Plugins（插件），默认开启的是git，你也可以按照 https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins 所示开启和查看

## 关闭崩溃报告

有时软件崩溃就已经很让人烦心了，重新打开后还弹出问题报告更是烦不胜烦，不过我们可以关闭它

```bash
defaults write com.apple.CrashReporter DialogType none        #关闭
defaults write com.apple.CrashReporter DialogType crashreport #重新开启
```

## 强制退出程序

有时候程序假死了，强行退出<span class="text-gray">（⌥option + 右键程序）</span>也没用，这时可以使用 `killall` 命令，以微信为例：

```bash
killall WeChat
```

## 程序栏恢复初始状态

将程序栏恢复为电脑刚刚激活时的状态

```bash
defaults delete com.apple.dock; killall Dock
```

这些 `defaults` 开始的指令，实际修改的是系统默认的 Plist 表单，这些表单管理着系统中全部程序的默认设置，上面所做的修改无非是改了某些程序的默认设置罢了。你也可到`/Library/Preferences/`和`/Users/你的用户名/Library/Preferences/`<span class="text-gray">（按住 ⌥option 点击访达的前往- 资源库）</span>内查看全部的 Plist 表单

## 文件格式转换

textutil -convert 命令可在 txt, html, rtf, rtfd, doc, docx, wordml, odt, webarchive 之间来回转换格式

```bash
textutil -convert ./a.txt ./b.docx
```

更多相关指令可查阅我在 [Github 上的 MacCommand 仓库](https://github.com/qLzhu/macCommand)

## 远程连接mac

前提需要先开启 "系统偏好设置 - 共享 - 远程登录"，再把允许访问更改为所有用户，再记住上方给你的IP地址，然后你就可以使用另一台电脑远程ssh连接你的mac啦