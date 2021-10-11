---
title: npx 跟 npm 有什么区别
abbrlink: 27748
date: 2021-10-11 18:23:35
categories: npm
tags: [npm, npx]
---

如果你对 npm 不陌生的话，应该知道 npm 是 JavaScript 世界的包管理器。让你在开发中，可以把想要的 Node 插件，通过 npm 安装到 local 或者 global 环境下。那么问题来了，如果你想测试某个插件，这个插件需要安装到全局环境下，你不想安装怎么办？那么此时 npx 来了。

{% note %}
  npx 是 npm v5.2.0 之后内建的指令
{% endnote %}

## 避免全局安装模块

这里我们以 hexo 静态博客框架为例，查看它的版本信息
<!-- more -->

npm 方式

```bash
npm install hexo -g
hexo --version
```

npx 方式

```bash
npx hexo --version

# hexo: 5.4.0
# hexo-cli: 4.3.0
# os: darwin 20.6.0 11.6
```

npm 会全局安装 hexo，你不删除的话，hexo 将会永久的储存在你本机 node_modules 目录下。如果你使用 npx 的话，它会下载到临时目录里，完成指令后会自动删除

## 调用项目安装的模块

一般来说需要全局安装的模块，只在项目下安装的话，要想调用只能通过配置 package.json 的 scripts 字段或者向下面方式执行

```bash
node_modules/.bin/hexo --version
```

要是你直接执行的话，终端会报错

```bash
hexo --version
# zsh: command not found: hexo
```

npx 就是想解决这个问题，让项目内部安装的模块用起来更方便，只要像下面这样调用就行了

```bash
npx hexo --version
```
原理很简单，npx 会到 node_modules 目录下和环境变量 `$PATH` 里面，检测 hexo 是否存在，如果都沒有，就会自行安裝

## 测试不同版本的模块

```bash
npx hexo@next
```

## 执行Github源码

Github 上的 repository 和 gists 可以通过 npx 直接执行，npm 则不行（远程代码必须是一个模块，即必须包含 package.json 和入口脚本）

```bash
npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32
npx github:piuccio/cowsay hello
```

## 拓展阅读

- [npx](https://github.com/npm/npx)
- [阮一峰](https://www.ruanyifeng.com/blog/2019/02/npx.html)
- [Elijah Manor](https://egghead.io/lessons/npm-introduction-to-execute-npm-package-binaries-with-the-npx-package-runner)
