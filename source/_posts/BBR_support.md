---
title: 在亚马逊aws服务器上添加Google BBR支持
date: 2018-07-22 18:26:38
categories:
- 旧文搬运
tags:
- 旧文搬运
- linux
---

[参考文章1](https://51.ruyo.net/2783.html)

[参考文章2](http://blog.csdn.net/VgFengYe/article/details/78609040 )

[官方 quick start文档](https://github.com/google/bbr/blob/master/Documentation/bbr-quick-start.md)

1、获取root权限：

```
sudo -s
```

2、根据官方文档，TCP BBR需要linux的内核版本为4.9或者更高版本，因此需要先检查系统内核：

```
#查看当前系统全部信息
uname -a
```

{% asset_img 1.png  系统信息 %}


显示系统内核为4.4.0，所以需要升级一下内核

在[这里](http://kernel.ubuntu.com/~kernel-ppa/mainline/)查看最新内核，根据系统架构下载最新的.deb文件

```
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.16-rc2/linux-image-4.16.0-041600rc2-generic_4.16.0-041600rc2.201802190311_amd64.deb
```
安装内核：

```
dpkg -i linux-image-4.16.0-041600rc2-generic_4.16.0-041600rc2.201802190311_amd64.deb
```

更新引导文件并且重启系统：

```
#更新grub系统引导文件
update-grub
#重启系统
reboot
```

重启后执行uname -r可以看到系统内核已经切换

{% asset_img 17.png  系统内核切换 %}


开启bbr：

```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
#保存
sysctl -p
```

提示permission denied，sudo -s获取root权限，再次执行操作,成功。

执行

```
sysctl net.ipv4.tcp_available_congestion_control
```
看到net.ipv4.tcp_available_congestion_control = reno cubic bbr

再执行

```
lsmod | grep bbr
```

看到tcp_bbr                20480  1

说明操作成功bbr已经启动