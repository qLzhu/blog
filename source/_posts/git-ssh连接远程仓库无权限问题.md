---
title: SSH连接远程仓库无权限问题
abbrlink: 53774
date: 2021-10-02 15:52:41
categories: git
tags: [git, Permission denied]
---

在连接 blog 在 Github 上的远程仓库时，终端提示：

```bash
Warning: Permanently added the RSA host key for IP address '**.205.**3.1*6' to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: 无法读取远程仓库。
```

随后通过 ssh 命令检测了下是否能够正常连接 Github，发现同样是没有权限。
<!-- more -->

```bash
ssh -T git@github.com
git@github.com: Permission denied (publickey).
```

那么这时肯定是密钥出现问题了!
通过 ls 命令才发现本地的 id_rsa 和 id_rsa.pub 没了，原因是之前我做过电脑更新！

```bash
ls -al ~/.ssh
drwx------   5 qinlzhu  staff   160 10  2 15:39 .
drwxr-xr-x+ 41 qinlzhu  staff  1312 10  2 15:57 ..
-rw-r--r--   1 qinlzhu  staff  2646 10  2 15:42 known_hosts
```

找到原因就好解决了，我们只需要通过 ssh-keygen 命令重新生成密钥，并把公钥部署到 GitHub 上即可。

```bash
ssh-keygen -t rsa -b 4096 -C ["email@example.com"]
pbcopy < ~/.ssh/id_rsa.pub
```

知识拓展：

- [git 基础知识](https://qlzhu.github.io/blog/39939/)
- [与 SSH 连接](https://docs.github.com/cn/authentication/connecting-to-github-with-ssh)
- [检查现有 SSH 密钥](https://docs.github.com/cn/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)