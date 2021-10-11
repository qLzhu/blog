---
title: NPM EAUDITNOPJSON 错误
abbrlink: 2777
date: 2020-04-22 16:43:43
categories: npm
tags:
  - EAUDITNOPJSON
  - EAUDITNOLOCK
---

执行 npm 命令时报 `npm ERR! code EAUDITNOPJSON` 错误，是因为项目下没有 package.json 文件造成的，执行 `npm init -y`

如果报的是 EAUDITNOLOCK 错误，请执行 `npm i --package-lock-only`，EAUDITNOLOCK 错误一般是本地没有 package-lock.json 或者 package-lock.json 起冲突造成的

拓展阅读：
- [EAUDITNOPJSON](https://docs.npmjs.com/auditing-package-dependencies-for-security-vulnerabilities)
- [EAUDITNOLOCK](https://blog.lishunyang.com/2021/01/how-to-use-package-lock-correctly.html)
