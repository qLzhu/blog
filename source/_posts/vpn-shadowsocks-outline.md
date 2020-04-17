---
title: 科学上网
categories: vpn
tags:
  - vpn
  - shadowsocks
  - outline
abbrlink: 64387
date: 2020-04-17 17:06:27
---

国内的国情确定了我们可浏览的网站，由于我们是开发者需要翻看很多国外技术网站，查看这些技术网站就需要到科学的技术。这里我们就介绍下 hosts、[lantern](https://github.com/getlantern/lantern)、[shadowsocks](https://shadowsocks.org/en/index.html)、[outline](https://getoutline.org/en/home) 的搭建和使用

## hosts

hosts（the static table lookup for host name 主机名查询静态表）用于补充或取代网络中DNS的功能。它储存的是计算机网络中各节点信息，负责将主机名映射到相应的IP地址，合理利用可提高域名的解析速度。**在以前通过该技术可以实现FQ目的，现在已不再适用，因为自2018年8月24号起，长城开始启用基于SNI检测和TCP连接重置等手段进行了封锁**

## lantern

[lantern（蓝灯）](https://github.com/getlantern/lantern)免费的全平台兼容的FQ软件，每月500M，每月月初自动重置

## shadowsocks

Shadowsocks是一种基于socks5协议的代理工具，分为客户端Clients和服务端Servers两部分，客户端Clients是运行在你电脑或者手机上的软件，服务端Servers是运行在海外服务器上的程序，搭建该服务需要先买台VM主机，我这里使用的是[vultr](https://www.vultr.com/)的服务器，系统采用的是 Ubuntu 19.10 x 64 版本

线上服务器搭建好后，在本地使用终端通过SSH命令，登陆到Vultr的云主机服务器上
```bash
ssh root@Vultr-OS-IP
```

### 安装组件

```bash
apt-get update
apt-get install python-pip
pip install shadowsocks
```

### 安装谷歌BBR加速器

```bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh 
chmod +x bbr.sh && ./bbr.sh
```

### 创建Shadowsocks服务配置文件

```bash
# 路径和名称可随意定义
vim /etc/shadowsocks.json
```

在shadowsocks.json文件内添加如下内容<span class="text-gray">（回车后英文状态下按 `i` 键进入编辑模式，编辑后按 `esc` 退出编辑模式， 输入 `:wq` 保存退出vim）</span>

```
{
  "server":"VPS IP",
  "local_address":"127.0.0.1",
  "local_port":1080,
  "port_password":{
    "9**0":"ss**",
    "9**1":"ss*1"
  },
  "timeout":300,
  "method":"aes-256-cfb",
  "fast_open": true
}
```

server：换成你服务器的IP
port_password：多用户使用时开放的端口及其密码
method：加密协议
fast_open：是否开启加速器

### 开启Shadowsocks服务

```bash
ssserver -c /etc/shadowsocks.json -d start

# 停止命令
# ssserver -c /etc/shadowsocks.json -d stop

# 调试时使用以下命令可实时预览请求信息
# ssserver -c /etc/shadowsocks.json
```

此时启动会报 `undefined symbol: EVP_CIPHER_CTX_cleanup` 错误，该问题主要openssl.py版本升级后造成的。只需要把openssl.py文件内的 `CIPHER_CTX_cleanup` 改成 `CIPHER_CTX_reset` 就可解决

```bash
# 如果终端里的 openssl.py 路径跟这里不一致
# 请改成终端错误信息内的路径
vim /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py

# 进入文件后，直接按 shift 输入如下指令
:%s/cleanup/reset/
:x
```

现在再使用 `ssserver -c /etc/shadowsocks.json -d start` 命令开启Shadowsocks服务即可完成！