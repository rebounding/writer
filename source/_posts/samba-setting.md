---
title: vbox 下 Ubuntu 与 Win7 的 samba 配置
date: 2016-01-17 17:41:42
categories:
- system
tags:
- samba
- vbox
---
近期需要新的开发环境，于是在笔记本上装了一个 vbox + Ubuntu 14.04 LTS。通常使用 vbox 与宿主共享文件都会使用 vbox 自带的共享文件夹功能，这次我使用 samba，通过在 Windows 上映射盘符来达到同样的目的。

要实现 Linux 系统与 Windows 系统之间的盘符映射，需要在 Linux 上安装 samba 并进行配置。

<!-- more -->

## 步骤

### 建立系统用户
通常用已有用户就行了，假设是 root。注意，这里的系统账户指的是 Linux 的，不是 samba。

### 创建 samba 帐户
``` bash
$ smbpasswd -a root
new SMB password:
Retype new SMB password:
```
这里的密码是用于 Windows 下登录 samba 服务的密码，可以是空。

### 配置 smb.conf
``` bash
[cipher]
  comment = mirror for Dev
  path = /
  public = yes
  browseable = no
  writable = yes
  create mask = 0644
  valid user = root
  write list = root
```
这里的 [cipher] 是使用 Windows 连接时的‘share’标识，valid user 对应 2. 中加入的 samba 账户。

### 重启 samba 服务
``` bash
$ service smb restart
```
如果是 smbd，可以使用
``` bash
$ /etc/init.d/smbd status
```
查看状态，显示
``` bash
* smbd is running
```
就说明没问题了。

### 在 Windows 下映射盘符
在资源管理器窗口输入 \\\\ip\cipher，弹出验证对话框， 输入 root 和密码。到这里正常情况下就可以看到 Windows 系统上多了一个盘符，映射到 samba 配置文件指定的位置。

## 常见问题
由于使用的是 vbox 建立的虚拟主机，映射时的 IP 如何填呢？
实际上只使用 NAT 的方式是不行的，需要另开一块网卡，使用‘Host only’模式。重启虚拟机使用 ifconfig 就能看到 eth1 (即是刚才新开的第二块虚拟网卡) 的 ip 地址，使用这个地址通过 Windows 做映射就可以了。

无法连通？
先看看是不是 Linux 的防火墙的问题，使用 iptable 命令操作。
