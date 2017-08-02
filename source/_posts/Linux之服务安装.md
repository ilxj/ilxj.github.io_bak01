---
title: Linux之服务安装
date: 2017-06-06 22:33:11
tags: [Linux,工具]
comments: false
---

# ssh-server 安装

**SSH分客户端**
```
openssh-client
```
**服务端**
```
openssh-server
```
<!--more-->
如果你只是想登陆别的机器的SSH只需要安装**openssh-client**__(Ubutntu有默认安装，如果没有则 **sudo apt-get install openssh-client**)__，如果要使本机开放SSH服务就需要安装**openssh-server**。

## 安装
我这里使用命令安装服务端SSH。
```
sudo apt-get install openssh-server
```
## 检查
然后使用命令确认sshserver是否启动了：
```
ps -e |grep ssh
```

如果看到sshd那说明ssh-server已经启动了。
```
alex@ubuntu:~$ ps -e | grep ssh
 2284 ?        00:00:00 ssh-agent
 8743 ?        00:00:00 sshd
```

如果没有则可以这样启动：
```
sudo /etc/init.d/ssh start
#或者
service ssh start
```

## 配置
配置文件位于**/etc/ssh/sshd_config**，在这里可以定义SSH的服务端口，默认端口是22。
然后重启SSH服务：
```
sudo /etc/init.d/ssh stop
sudo /etc/init.d/ssh start
```
## 开机自启动配置
设置开机自动启动ssh服务：
Ubuntu中配置openssh-server开机自动启动打开/etc/rc.local文件，添加如下语句：
```
/etc/init.d/ssh start
```