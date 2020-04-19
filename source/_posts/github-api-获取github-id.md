---
title: API 获取 Github ID
abbrlink: 8621
date: 2020-04-18 18:19:33
categories: github
tags:
  - Github ID
---

获取 Github 当前账户的 ID，可使用官方给出的 API 是 `https://api.github.com/users/github`，把最后的 Github 换成你的用户名，直接在浏览器地址栏中输入回车即可获取，输出如下：

```json
{
  "login": "qLzhu",
  "id": 1***59,
  "node_id": "MDQX****DIzMzU5",
  "gravatar_id": "",
  ...
}
```
