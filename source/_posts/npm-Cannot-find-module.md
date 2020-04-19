---
title: NPM Cannot Find Module
abbrlink: 21517
date: 2020-04-18 19:01:41
categories: npm
tags: 
  - npm
  - Cannot find module
---

执行 `npm install` 时终端提示 Cannot find module '../lib/utils/unsupported.js' 错误，解决方案就是删除 /usr/local/lib/node_modules/npm 目录！
```bash
sudo rm -rf /usr/local/lib/node_modules/npm

brew reinstall node
nvm reinstall-packages #使用nvm管理Node的使用此命令
```