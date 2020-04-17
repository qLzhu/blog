---
title: SSH Connection closed
categories: mac
tags:
  - ssh
  - ssh Connection closed
abbrlink: 11331
date: 2020-04-17 14:09:00
---

Mac终端连接远程服务器时，报 `Connection closed by 207.***.8*.** port 22` 错误！原因是服务器账户的密码换了，链接时匹配本地的 ECDSA，未匹配正确，所以服务器拒绝建立连接

ssh-keygen 命令删除指定密钥，再重新连接输入密码即可解决

```bash
# 注意把 YourName 换成你的电脑的用户名
# 服务的 207.***.8*.** IP 也需要换成你的
ssh-keygen -f "/users/YourName/.ssh/known_hosts" -R 207.***.8*.**
```

当然你还可以手动删除 `known_hosts` 文件内的密钥，在你的根目录下找到 `.ssh/known_hosts`（<span style="color: #999">显示隐藏文件快捷键Command + shift + .</span>） ，删除 `207.***.8*.**` 开头的一行，然后保存退出！
