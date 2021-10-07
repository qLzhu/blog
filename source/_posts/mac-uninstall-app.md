---
title: MacOS Mdfind Command
abbrlink: 21969
date: 2020-04-17 15:30:44
categories: mac
tags:
  - node
  - npm
  - mdfind
---

Mac 上我们不借助第三方应用删除 App 的话，通常是直接移除到垃圾篓，或者按住 `⌥option` 删除<span class="text-gray">（App Store 下载的 App）</span>，时间长了系统会帮我们自动清理一些App残留。但是有些文件系统是不删除的，这时我们可以借助 `mdfind` 命令进行查询下跟 App 有关的文件，例如我们删除 CleanMyMac

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

卸载 NodeJS 从官网下载的安装
如果未正常卸载的使用 `which` 命令查看下，安装的位置是不是自己更改过

```bash
sudo rm -rf /usr/local/{bin/{node,npm},lib/node_modules/npm,lib/node,share/man/*/node.*}
```