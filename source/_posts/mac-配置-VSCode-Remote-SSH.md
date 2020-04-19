---
title: Mac 配置 VSCode Remote-SSH
abbrlink: 15892
date: 2020-04-18 17:31:52
categories: mac
tags:
  - VSCode
  - remote-ssh
---

微软2019年05月03日，在PyCon 2019大会上推出了三个非常实用的功能性插件。它们可以帮助开发者在容器、物理机器或虚拟机，以及Windows Subsystem for Linux (WSL)中实现无缝的远程开发

- `Remote-SSH`：连接虚拟/实体Linux主机
- `Remote-Containers`：连接开发容器（例如：Docker）
- `Remote-WSL`：连接WSL（也就是Linux子系统）

所以推出这个三个插件的重点在于服务使用Windows系统但有Linux开发需求的用户群体
<!-- more -->

## 前言

这篇文章只介绍`Remote-SSH`的配置与使用，因为`Remote-Containers`和`Remote-WSL`我还未使用到，所以想了解它们的朋友可以参考[VSCode官网](https://code.visualstudio.com/docs/remote/remote-overview)给出的详情文档，也可以自动百度或者谷歌相关文章！以下是测试环境和工具

- MacOS
- VSCode
- 远程主机：CentOS Linux release 7.4.1708 (Core)

## 安装

打开VSCode软件，点击最左侧活动栏内的 **“扩展”** 小图标，然后搜索 **`Remote SSH`** 并安装即可。安装完成后在 **“扩展”** 的下方会多出一个类似电脑屏幕的小图标

{% asset_img install.png Install Remote SSH 界面 %}

## 配置密钥

### 创建密钥

使用[ssh-keygen](http://man.linuxde.net/ssh-keygen)命令创建的密钥，会在本地`~/.ssh/`的目录内生成两个文件`id_rsa-remote-ssh`和`id_rsa-remote-ssh.pub`，我们要部署的是`id_rsa-remote-ssh.pub `公钥内容

```
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa-remote-ssh
```

{% note %}
参数说明
-t 指定要创建的类型
-b 密钥长度
-f 指定用来保存密钥的文件名
id_rsa-remote-ssh 名字可以随意定义
{% endnote %}

## 上传公钥到远程服务器

1. mac拷贝公钥命令 `pbcopy < ~/.ssh/id_rsa-remote-ssh.pub`
2. ssh登陆远程服务器
3. 创建 authorized_keys 文件，并把刚才使用pbcopy命令拷贝的公钥内容，粘贴到此文件内保存
4. 去除 /etc/ssh/sshd_config 内 `PubkeyAuthentication` 前的 # 号
5. `systemctl restart sshd.service` 重启远程服务器的ssh服务

## 配置Remote-SSH插件

按照图片所示，在用户级目录下的 .ssh 内创建一个配置文件（即/Users/your-host-name/.ssh/config），并在该文件内填写相应内容

{% asset_img Remote-SSH-config.png 创建配置文件 Config 图片 %}
{% asset_img example-config.png 配置 config 图片 %}

如果你想连接多台远程服务器，继续在后面追加配置内容即可

```bash
Host xxx                             # xxx 自定义连接名称
    User root                        # root 远程服务器账户名称
    HostName 47.92.xxx.18x           # 47.92.xxx.18x 远程服务器地址
    IdentityFile ~/.ssh/id_rsa_xxxx  # ~/.ssh/id_rsa_xxxx 本地密钥所在位置
```

## 连接远程服务器

在VSCode左侧 “CONNECTIONS” 下找到你刚才创建的服务器名称，然后右键选择打开的方式

{% note %}
Connect to Host in Current Window 在当前窗口中连接到主机
Connect to Host in New Window 在新窗口中连接到主机
{% endnote %}

{% asset_img new-window.png 创建配置文件 Config 图片 %}
{% asset_img succeed_.png 配置 config 图片 %}

打开任意文件编辑后保存，它就会自动保存到远程服务器了(^_^)