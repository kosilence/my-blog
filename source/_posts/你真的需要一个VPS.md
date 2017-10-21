---
title: 你真的需要一个VPS
date: 2017-04-28 19:34:12
desc: 初始VPS SSH免密登录 科学上网 Docker BBR
---

VPS：我要给你整个世界！

我：那你整吧。

<!--more-->

-------

## 选择、购买、创建

#### 选择公司
国内：阿云里、腾讯云、景安

国外：[Vultr](http://www.vultr.com/?ref=7099343)、[Linode](https://www.linode.com/?r=b9e1ce1ebdbb3b17ce134eb53a5f1f6437f54b54)、[SugarHosts](http://www.sugarhosts.com/zh-cn/)、[DigitalOcean](https://www.digitalocean.com/)

**大部分公司都有优惠活动，买之前记得搜索相关资料哦**
***
#### 选择架构
**OpenVZ**
价格便宜，大部分已经超售，和其他人共用虚拟空间，不支持Docker等，Pass！

**KVM**
较独立的虚拟化架构，自由，价格适中，支持Docker，BBR等

**Xen**
没怎么见过

**喜欢折腾无束缚，让我们选择KVM架构！**
***
#### 选择机房
**Ping值**
本地网络环境`ping HOST` && [站长工具](http://ping.chinaz.com/)

**Speed**
这项由本地网络环境和VPS共同决定，`wget something.big`，例如[Linode测试地址](https://www.linode.com/speedtest)

**路由追踪**
查看从某节点至VPS所经过的所有路由，有一些新加坡的节点要绕道美国节点，所以稳定性和速度会受到影响，工具：[站长工具路由追踪](http://tool.chinaz.com/tracert/)、[BestTrace](http://www.ipip.net/download.html#ip_trace)

**测试为主**
***
#### 购买
购买国内的VPS就不说了，国内支付很简单

购买国外的VPS，最通用的是外币信用卡，同时也有一些公司支持Paypal、支付宝、比特币等方式
***
#### 创建VPS

选择VPS配置 --> Linux系统 --> 分配空间(个别) --> 设置Root密码(自动或手动) --> 启动 --> 获取VPS IP地址 --> GO

***
## 登录你的VPS
登录非常简单，本地终端输入：

```
$ ssh root@host
```
默认登录22端口，如果需要指定端口，可以这样登录

```
$ ssh -p 2222 user@host
```
第一次登陆主机，会出现验证host主机真实性的公钥指纹，如果谨慎一点的话可以去官网查看

然后输入密码登陆成功

附录：[ssh教程](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)

***
#### ssh 公钥免密登录
这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接生成一个：
本地终端执行

```
$ ssh-keygen
```
一路回车

```
$ ssh-copy-id user@host
```
从此你再登录，就不需要输入密码了
***
#### 添加 alias 简易命令
虽然已经设置了免密登录，但每次登录 vps 都要输入 host

```
$ ssh user@host
```
让我们配置一个 alias，使登录 vps 变得更方便快捷

```
$ vim ~/.bashrc
```
文件内添加以下内容，(user 一般为 root，host是你vps的ip地址或者域名)

```
alias sshvps='ssh user@host'
```
保存，返回，然后重新加载一下配置

```
$ source ~/.bashrc
```
搞定，从此以后本地登录VPS只需一条简单的命令即可

```
$ sshvps
```
***
## 配置VPS
#### 关闭 SSH 登录
因为现在我们是通过本地机器的秘钥登录，所以让我们彻底关闭SSH登录，可以预防绝大部分的入侵攻击

登录VPS，编辑ssh配置文件

```
$ vim /etc/ssh/sshd_config
```
找到`#PasswordAuthentication yes`修改为`no`

```
#PasswordAuthentication no
```
保存文件，退出，重启SSH服务

```
$ /etc/init.d/ssh restart
```

***
#### 安装 CSF 防火墙，屏蔽尝试入侵服务器的 IP
可用可不用，简单的几个命令就可以完成

```
$ cd /etc
$ rm -fv csf.tgz
$ wget http://download.configserver.com/csf.tgz
$ tar -xzf csf.tgz
$ cd csf
$ sh install.sh
```
检查 csf 是否已经安装正确并运行

```
$ perl /usr/local/csf/bin/csftest.pl
```
可以配置`/etc/csf/csf.allow`或者`/etc/csf/csf.ignore`，防止系统错误屏蔽本地IP

添加本地IP完成后，运行`csf -r`重启防火墙即可

***

#### 科学地上网
**让我们先装一个 Docker 吧**

```
$ curl -sSL https://get.docker.com/ | sh
```
安装完成后，查看一下版本

```
$ docker --version
```

然后开启 `docker`

```
$ service docker start
```

OK，然后让我们去`docker hub`找一找`shadowsocks`

![](http://ww1.sinaimg.cn/large/9eb6a82aly1ffvhr2q0x5j20zu08wwf9.jpg)

第一个看起来就不错的样子，点开把镜像`pull`下来

```
$ docker pull mritd/shadowsocks
```
按照说明，把容器跑起来，这里我添加了`--restart=always`选项，以防服务器重启之后容器关闭，无法连接，记得把密码改一下

```
$ docker run -dt --restart=always --name ss -p 6443:6443 mritd/shadowsocks -s "-s 0.0.0.0 -p 6443 -m aes-256-cfb -k password123 --fast-open"
```
查看一下容器的运行情况，容器跑起来即可

```
$ docker ps
```

本地我们可以使用**ShadowsocksX**客户端软件，Mac版本下载地址：[Shadowsocks for OSX](https://github.com/shadowsocks/shadowsocks-iOS/wiki/Shadowsocks-for-OSX-%E5%B8%AE%E5%8A%A9)

本地的客户端怎么配置自己看说明就好了，大概如下图所示

![](http://ww1.sinaimg.cn/large/9eb6a82aly1ffvhrkoij9j20j80bm752.jpg)


先尝试一下全局模式，浏览器打开**Google**试试？

#### 是不是特别慢。。。

再返回VPS服务器端，看一下这个`mritd/shadowsocks` Docker 镜像的说明

原来这个镜像还包含了与**锐速**功能相似的**kcptun**，这两种vpn加速方式并不优雅，通过多倍的数据流量来提高连接速度

**锐速**作为国产闭源软件，可信度不高

**kcptun**使用的是双边加速的模式，即你的服务器端和客户端需同时运行一个进程

![](http://ww1.sinaimg.cn/large/9eb6a82aly1ffvhrwje7nj20mw03ht8r.jpg)

想了想，都不太合适，我们还是先用一下**BBR**加速吧

**BBR**

1. 不支持 OpenVZ
2. 需要 Linux 内核版本大于等于**4.9**
3. 某些厂商的Linux内核是改造过的，可能需要先把VPS配置为自定义内核，一般厂商会有此类情况的说明文档，例如 (**Linode、DigitalOcean**)

强烈推荐： [一键安装最新内核并开启 BBR 脚本 -- 秋水逸冰](https://teddysun.com/489.html)

**Debian 8 / Ubuntu 14**

如果内核低于**4.9**的话，升级内核至 **4.10**

```
$ wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.10.9/linux-image-4.10.9-041009-generic_4.10.9-041009.201704080516_amd64.deb
```
安装内核

```
$ dpkg -i linux-image-4.*.deb
```
删除旧的内核

```
dpkg -l|grep linux-image
apt-get purge 旧内核的名字
```
更新 grub 系统引导，并且重启VPS

```
$ update-grub
$ reboot
```
开启 BBR

```
$ uname -r
```
查看内核版本，4.10 即可，执行如下命令

```
$ echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
$ echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
$ sysctl -p
```
写入 BBR 配置，并保存

查看BBR是否安装成功，返回值包含BBR即可

```
$ lsmod | grep bbr
```

