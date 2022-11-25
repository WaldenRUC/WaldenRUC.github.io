---
title: 'Linux DevOps'
date: 2022-11-25
permalink: /posts/2012/08/blog-post-4/
tags:
  - Linux
  - Python
---

## Q: Python pip下载第三方库速度太慢

A: pip install <pkg-name> -i https://pypi.tuna.tsinghua.edu.cn/simple

***

## Q: 终端连接服务器总是断开？

A: 

```
sudo vi /etc/ssh/sshd_config
```
将【ClientAliveInterval 0】改为 【ClientAliveInterval 30】

将【ClientAliveCountMax 3】 改为 【ClientAliveCountMax 86400】

```
sudo service sshd restart
```

***

## Q: Ubuntu无法使用jps命令

A:

ubuntu下没有jps命令：安装openjdk-17-jdk-headless解决（具体版本依据服务器的java版本确定

***

## Q: 无法访问hadoop启动的网页

A:

因为默认开启了**防火墙**。

```
CentOS:
sudo systemctl disable firewalld.service
service network restart

Ubuntu:
sudo ufw disable
reboot
```
操作结束后即可通过网页访问

***

## Q: ssh无法连接远程服务器(192.168.199.1)

A: 无法连接服务器可能是因为**ssh的端口被block**，因此需要从**物理管理端口iBMC**进入服务器：用KVM软件登录【192.168.30.115】，用/密【root/goldtech】
切换到root用户，关闭ufw防火墙：【ufw disable】，并【reboot】重启即可

***

## Q: 更改CentOS的IP地址

A: CentOS的IP地址可以通过【ip addr】来设置。

网卡配置文件在【/etc/sysconfig/network-scripts】中，如果网卡是eth0，则编辑【vi ifcig-eth0】，进入文件。

假设要修改的IP为【192.168.199.1】，则修改：
```
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.199.1
GATEWAY=192.168.199.254
NETMASK=255.255.255.0
DNS1=8.8.8.8
DNS2=119.29.29.29
```

最后输入【sudo service network restart】重启服务

***

## Q: 如何在服务器A上免密登录服务器B

A: 在A上运行ssh-keygen，在~/.ssh/下找到id_rsa.pub，将其上传到
服务器B的~/.ssh/authorized_keys文件末尾

***

## Q: bash脚本单引号和双引号的区别

A: 单引号显示的是【纯文本】，而双引号会显示引号内的【变量】对应的值

例如：
```
echo '$LANG' 与 echo "$LANG"
```
前者显示的是"\$LANG"，后者显示的是"zh-CN"

***

