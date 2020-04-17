---
title: xcode-select --install
categories: mac
tags:
  - mac
  - mac-xcode-select --install
abbrlink: 35871
date: 2020-04-17 13:32:37
---

如果你是程序猿并且使用的是 Mac 的话，肯定避免不了要安装 Xcode，因为它内置了很多开发必须的环境，在最新的版本中 Xcode 默认不再集成 `Xcode command line tools`。如果你不是一名 iOS 或 OS X 开发者，可以跳过 XCode（软件太大了近10G）直接安装 `Xcode command line tools` 了

Install 命令

```
xcode-select --install
```

如果 Install 不了，可直接到 [`Developer Apple`](https://developer.apple.com/download/more/) 内下载相应的包

如果你想了解 `Xcode command line tools` 包含多少可用的命令，可以到 `/Library/Developer/CommandLineTools/` 查看，另外如果你想卸载的话直接删除该目录即可