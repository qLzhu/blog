---
title: NPM EAUDITNOPJSON 错误
abbrlink: 2777
date: 2020-04-22 16:43:43
categories: npm
tags:
  - EAUDITNOPJSON
  - EAUDITNOLOCK
---

执行 `npm audit fix` 时报npm ERR! code EAUDITNOPJSON错误，这是因为项目下没有package.json文件造成的，执行 `npm init -y` 即可解决！ https://docs.npmjs.com/auditing-package-dependencies-for-security-vulnerabilities

如果你还报EAUDITNOLOCK错误的话，请执行 `npm i --package-lock-only`
