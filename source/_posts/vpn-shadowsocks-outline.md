---
title: 搭建 VPN 服务
categories: vpn
tags:
  - vpn
  - shadowsocks
  - outline
abbrlink: 64387
date: 2020-04-17 17:06:27
---

国内的国情确定了我们可浏览的网站，由于我们是开发者需要翻看很多国外技术网站，查看这些技术网站就需要到科学的技术。这里我们就介绍下 hosts、ssh、[lantern](https://github.com/getlantern/lantern)、[shadowsocks](https://shadowsocks.org/en/index.html)、[outline](https://getoutline.org/en/home) 的搭建和使用

## hosts

hosts（the static table lookup for host name 主机名查询静态表）用于补充或取代网络中DNS的功能。它储存的是计算机网络中各节点信息，负责将主机名映射到相应的IP地址，合理利用可提高域名的解析速度。**在以前通过该技术可以实现FQ目的，现在已不再适用，因为自2018年8月24号起，长城开始启用基于SNI检测和TCP连接重置等手段进行了封锁**
<!-- more -->

## ssh

使用ssh端口映射也可达到FQ的目的。ssh端口映射、shadowsocks和outline这三种方式，都需要你有台境外服务器！这里推荐两个服务器提供商 [vultr](https://www.vultr.com/) 和 [搬瓦工](https://bandwagonhost.com/)，它们都支持微信和支付宝付款，两者的区别在于：

- [搬瓦工](https://bandwagonhost.com/)：按月或者年进行扣费；换IP需要缴纳多余的费用
- [vultr](https://www.vultr.com/)：按小时进行扣费；换IP选择个地区重建个服务器镜像即可

我自己就是使用的vultr，并且官网的UI设计的比较不错！我搭建的VPS服务器镜像地区是日本，系统是Ubuntu 19.10 x 64<span class="text-gray">（搭建镜像时尽量选择离你比较近的地区，同时记得要开启IP6协议哦）</span>。如何搭建VPS服务请参考https://www.stackcc.com/2019/07/25/vultrdas/ 和 https://github.com/Alvin9999/new-pac/wiki/%E8%87%AA%E5%BB%BAss%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%99%E7%A8%8B 太简单这里不赘述

VPS服务搭建好后，输入如下指令回车，并输入VPS服务器的密码
```bash
ssh -D127.0.0.1:9000 root@remote_host
```
- -D 动态转发，远程主机通过SOCKS协议连接到你要访问的网站，有加密作用
- 127.0.0.1:9000 是本地的IP和端口<span class="text-gray">（端口随便定义，一般选用 1024-65535 之间的并且尚未使用的端口号）</span>
- root 远程主机用户名<span class="text-gray">（一般默认的都是这个，即我们刚搭建的VPS服务器镜像）</span>
- remote_host 远程主机的IP地址

连接成功后，我们打开 **“系统偏好设置 - 网络 - 当前正在使用的网络<span class="text-gray">（例如：WIFI）</span>- 高级<span class="text-gray">（右下角）</span>- 代理 - SOCKS代理 - SOCKS代理服务器处填写上127.0.0.1:9000<span class="text-gray">（端口写你自己定义的）</span> - 好 - 应用”**

此时你再查看下自己的[IP地址](https://www.ip.cn/)是不是变成VPS服务的了！访问一些被墙的网站也访问通了！终端内退出服务器的话，ssh连接就会断开

{% note %}
想了解更多ssh转发知识请参考：
https://www.ssh.com/ssh/
https://man.linuxde.net/ssh
https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/index.html
https://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html
{% endnote %}

## lantern

[lantern（蓝灯）](https://github.com/getlantern/lantern)免费的全平台兼容的FQ软件，每月500M，每月月初自动重置

## shadowsocks

Shadowsocks是一种基于socks5协议的代理工具。客户端下载列表如下：

- [Mac](https://github.com/shadowsocks/ShadowsocksX-NG/releases)
- [window](https://github.com/shadowsocks/shadowsocks-windows/releases)
- [Android](https://github.com/shadowsocks/shadowsocks-android/releases)
- iPhone <span class="text-gray">（版本的客户端是 Shadowrocket 需要国外的 Apple ID 才可下载，并且该软件是收费的）</span>

VPS服务搭建这里直接跳过，不懂的请[参考ssh端口映射](#ssh)的那一步。通过本地的终端ssh连接到远程服务器上
```bash
ssh root@remote_host
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
  "server":"remote_host",
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

### 客户端配置

现在服务器端已经配置完毕，紧接着就是配置客户端。Shadowsocks客户端的设置及其使用非常简单。在客户端中“添加服务器”，正确填写服务器的地址、端口、密码和加密方式，然后就可以连接服务端了

## Outline

Outline 是 Jigsaw 团队开发的，而 Jigsaw 又是谷歌母公司 alphabet 旗下的，开源口号好像是为全球媒体工作者提供帮助什么的，具体的没记清楚。反正跟 shadowscoks 是同类型的工具，都是用来帮助我们科学上网的。软件核心部分依赖的还是 shadowscoks，只不过在此基础上重新包装开发而已。选择它的目地，主要是国内各应用商城内依然可下载 Outline App。使用 Outline 服务需要服务端和客户端两个软件配合使用，即

- 服务端：Outline Manager
- 客户端：Outline

第一步跟配置Shadowsocks一样，都是先在Vultr搭建服务器，然后使用ssh再登陆上去

### 部署方案

{% tabs Outline 服务器部署方案 %}
  <!-- tab Docker方式部署 -->

登陆成功后在终端分别执行下述命令

```bash
apt update
apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
apt update
apt-cache policy docker-ce
apt install docker-ce
```

完成后用`systemctl status docker`命令检测，如出现以下类似界面，说明 Docker 部署已成功

```bash
docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2018-07-05 15:08:39 UTC; 2min 55s ago
     Docs: https://docs.docker.com
 Main PID: 10096 (dockerd)
    Tasks: 16
   CGroup: /system.slice/docker.service
           ├─10096 /usr/bin/dockerd -H fd://
           └─10113 docker-containerd --config /var/run/docker/containerd/containerd.toml
```

配置 Outline Manager

到 [Outline](https://getoutline.org/en/home) 官网选择系统相对应的版本，分别下载下 outline Manager 和 Outline。完成后先打开 Outline Manager 服务端软件，进入软件后点击界面右下脚的“**随时随地安装 outline**”选项，然后**复制第一段提示你安装 shadowsocks 服务的代码，粘贴到服务器内进行安装**，即下述代码

```bash
bash -c "$(wget -qO- https://raw.githubusercontent.com/Jigsaw-Code/outline-server/master/src/server_manager/install_scripts/install_server.sh)"
```

安装完成后服务器终端提示信息内，会给出以”apiUrl”开头的一段字符串，复制该字符串粘贴到 Outline Manager 内（就是你复制服务器端安装shadowsocks的那块）

```
{"apiUrl":"https://***.28.*1.**9:***69/*********mfPAusF9w",
"certSha256":"FFFA7***************5B61976F57B4B1E12BB9***19772F6"}
```
  <!-- endtab -->
  <!-- tab 手动开启Firewall防火墙方式 -->
连接到服务器后在终端执行安装 outline 服务的命令

```bash
sudo bash -c "$(wget -qO- https://raw.githubusercontent.com/Jigsaw-Code/outline-server/master/src/server_manager/install_scripts/install_server.sh)"
```

执行成功后会有下述提示

{% asset_img 846459FAE33F2E2E514COFOESCI.png apiUrl值 %}

我们把”apiUrl（上图绿色的部分）”开头的一段字符串复制，然后粘贴到 Outline Manager 内（就是你复制服务器端安装shadowsocks的那块）

注意：
这两个端口需要牢记，我们待会手动开防火墙端口时会用到这两个端口
- Management port 32135, for TCP
- Access key port 60302, for TCP and UDP

回到 vultr 服务器管理界面

{% asset_img home.jpg 服务器管理界面 %}

进到该实例管理界面，依次点击 Settings -> + -> Add Firewall Group -> Manage Firewall Group

{% asset_img DServerInformation.jpg Firewall管理界面 %}
{% asset_img AddFirewallGroup.jpg Add Firewall Group管理界面 %}
{% asset_img ManageFirewallGroup.jpg Manage Firewall Group管理界面 %}

分别在 IPv4 Rules 和 IPv6 Rules 添加下述规则

- Protocol：SSH / Port(or range)：22
- Protocol：TCP / Port(or range)：32135
- Protocol：TCP / Port(or range)：60302
- Protocol：UDP / Port(or range)：32135

最后点 Linked Instances 把这些规则连接到实例上

{% asset_img LinkedInstances.jpg Linked Instances管理界面 %}

然后我们回到该实例管理界面，依次点击 Settings -> Firewall

{% asset_img ServerFirewall.jpg 防火墙管理界面 %}
{% asset_img UpdateFirewallGroup.jpg 防火墙应用界面 %}

至此防火墙配置完成
重新使用 ssh 命令连接到该实例，安装谷歌BBR加速器

```bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```

重启 VPS，检测 BBR 是否开启

```bash
lsmod | grep bbr
```

出现 tcp_bbr 即说明 BBR 已经启动
  <!-- endtab -->
{% endtabs %}


### 配置 outline

完成 Outline Manager 服务端的配置后，进入该服务的管理界面，在该界面内选择“添加新密钥”，然后把新创建的密钥共享给 outline App 使用。注意使用的主要是以 ss 开头的字符串哦！操作还有迷糊的地方，可点击该文章 [“Outline 的部署和使用”](https://oracleblog.org/its-my-life/how-to-deploy-outline-by-jigsaw/) 看图文进行操作

## 使用总结

长城防火墙封锁比较严重时或者不想付费的使用lantern。不严重使用shadowsocks或者outline。shadowsocks配置稍复杂些，严查期间封锁shadowsocks也要比outline严重。短时间使用那就选ssh端口映射。

同时要提醒大家的是：
<p style="color: #f2777a;text-align: center;">科学上网别信谣、别传谣、别造谣、别看不该看的东西、让我们做个守法的好公民！</p>