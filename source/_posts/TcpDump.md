---
title: tcpDump
date: 2017-07-01 19:05:11
tags: [抓包工具,tcp]
categories: 工具
top: 1
---
- - -
## Linux 混杂模式设置
**1. 查看网卡混杂模式状态**
```
alex@ubuntu:~$ ifconfig eth0

eth0      Link encap:Ethernet  HWaddr 00:0c:29:01:02:03
          inet addr:10.211.55.3  Bcast:10.211.55.255  Mask:255.255.255.0
          inet6 addr: fdb2:2c26:f4e4:0:49c8:a8e5:be6:a7f9/64 Scope:Global
          inet6 addr: fdb2:2c26:f4e4:0:20c:29ff:fe01:203/64 Scope:Global
          inet6 addr: fe80::20c:29ff:fe01:203/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1735 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1780 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:1451555 (1.4 MB)  TX bytes:193802 (193.8 KB)
```
<!--more-->

**2. 启动混杂模式**
主要是这个:**PROMISC**
```
alex@ubuntu:~$ sudo ifconfig eth0 promisc
alex@ubuntu:~$ ifconfig eth0
eth0      Link encap:Ethernet  HWaddr 00:0c:29:01:02:03
          inet addr:10.211.55.3  Bcast:10.211.55.255  Mask:255.255.255.0
          inet6 addr: fdb2:2c26:f4e4:0:49c8:a8e5:be6:a7f9/64 Scope:Global
          inet6 addr: fdb2:2c26:f4e4:0:20c:29ff:fe01:203/64 Scope:Global
          inet6 addr: fe80::20c:29ff:fe01:203/64 Scope:Link
          UP BROADCAST RUNNING PROMISC MULTICAST  MTU:1500  Metric:1
          RX packets:1742 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1782 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:1452336 (1.4 MB)  TX bytes:194198 (194.1 KB)
```
**3. 关闭混杂模式**
```
alex@ubuntu:~$ sudo ifconfig eth0 -promisc
alex@ubuntu:~$ ifconfig eth0
eth0      Link encap:Ethernet  HWaddr 00:0c:29:01:02:03
          inet addr:10.211.55.3  Bcast:10.211.55.255  Mask:255.255.255.0
          inet6 addr: fdb2:2c26:f4e4:0:49c8:a8e5:be6:a7f9/64 Scope:Global
          inet6 addr: fdb2:2c26:f4e4:0:20c:29ff:fe01:203/64 Scope:Global
          inet6 addr: fe80::20c:29ff:fe01:203/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1766 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1796 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:1455202 (1.4 MB)  TX bytes:195410 (195.4 KB)
```
## TcpDump
>- tcpdump存在于基本的FreeBSD系统中，由于它需要将网络界面设置为混杂模式，普通用户不能正常执行，但具备root权限的用户可以直接执行它来获取网络上的信息。因此系统中存在网络分析工具主要不是对本机安全的威胁，而是对网络上的其他计算机的安全存在威胁。
- 显然为了安全起见，不用作网络管理用途的计算机上不应该运行这一类的网络分析软件，为了屏蔽它们，可以屏蔽内核中的bpfilter伪设备。一般情况下网络硬件和TCP/IP堆栈不支持接收或发送与本计算机无关的数据包，为了接收这些数据包，就必须使用网卡的混杂模式，并绕过标准的TCP/IP堆栈才行。在FreeBSD下，这就需要内核支持伪设备bpfilter。因此，在内核中取消bpfilter支持，就能屏蔽tcpdump之类的网络分析工具。
- 并且当网卡被设置为混杂模式时，系统会在控制台和日志文件中留下记录，提醒管理员留意这台系统是否被用作攻击同网络的其他计算机的跳板。
### 命令格式

tcpdump采用命令行方式，它的命令格式为：
    tcpdump [ -adeflnNOpqStvx ] [ -c 数量 ] [ -F 文件名 ]
　　　　　　　　　　[ -i 网络接口 ] [ -r 文件名] [ -s snaplen ]
　　　　　　　　　　[ -T 类型 ] [ -w 文件名 ] [表达式 ]
　　　　　　　　　　
### 命令参数

|命令字|描述|
|:----:|---|
|-a |　　　将网络地址和广播地址转变成名字；
|-d |　　　将匹配信息包的代码以人们能够理解的汇编格式给出；
|-dd| 　　 将匹配信息包的代码以c语言程序段的格式给出；
|-ddd|     将匹配信息包的代码以十进制的形式给出；
|-e| 　　　在输出行打印出数据链路层的头部信息；
|-f| 　　　将外部的Internet地址以数字的形式打印出来；
|-l| 　　　使标准输出变为缓冲行形式；
|-n| 　　　不把网络地址转换成名字；
|-t| 　　　在输出的每一行不打印时间戳；
|-v| 　　　输出一个稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息；
|-vv| 　　 输出详细的报文信息；
|-c| 　　　在收到指定的包的数目后，tcpdump就会停止；
|-F| 　　　从指定的文件中读取表达式,忽略其它的表达式；
|-i| 　　　指定监听的网络接口；
|-r| 　　　从指定的文件中读取包(这些包一般通过-w选项产生)；
|-w| 　　　直接将包写入文件中，并不分析和打印出来；
|-T| 　　　将监听到的包直接解释为指定的类型的报文，常见的类型有rpc （远程过程调用）和snmp（简单网络管理协议；）

###  tcpdump的表达式介绍

>表达式是一个正则表达式，tcpdump利用它作为过滤报文的条件，如果一个报文满足表达式的条件，则这个报文将会被捕获。如果没有给出任何条件，则网络上所有的信息包将会被截获。在表达式中一般如下几种类型的关键字。

#### 第一种是关于类型的关键字
主要包括host，net，port, 例如 host 210.27.48.2，指明210.27.48.2是一台主机，net 202.0.0.0 指明 202.0.0.0是一个网络地址，port 23指明端口号是23。如果没有指定类型，缺省的类型是host.

#### 第二种是确定传输方向的关键字
主要包括src , dst ,dst or src, dst and src,这些关键字指明了传输的方向。举例说明，src 210.27.48.2 ,指明ip包中源地址是210.27.48.2 , dst net202.0.0.0 指明目的网络地址是202.0.0.0 。如果没有指明方向关键字，则缺省是src or dst关键字。

#### 第三种是协议的关键字
主要包括fddi,ip,arp,rarp,tcp,udp等类型。Fddi指明是在FDDI(分布式光纤数据接口网络)上的特定的网络协议，实际上它是"ether"的别名，fddi和ether具有类似的源地址和目的地址，所以可以将fddi协议包当作ether的包进行处理和分析。其他的几个关键字就是指明了监听的包的协议内容。如果没有指定任何协议，则tcpdump将会监听所有协议的信息包。

#### 其他关键字
除了这三种类型的关键字之外，其他重要的关键字如下：gateway,broadcast,less,greater,还有三种逻辑运算，取非运算是 'not ' '! ',与运算是'and','&&';或运算 是'or','││'；这些关键字可以组合起来构成强大的组合条件来满足人们的需要，下面举几个例子来说明。

- A想要截获所有210.27.48.1 的主机收到的和发出的所有的数据包：
```
#tcpdump host 210.27.48.1
```

- B想要截获主机210.27.48.1 和主机210.27.48.2 或210.27.48.3的通信，使用命令：(在命令行中使用括号时，一定要添加'\')
```
#tcpdump host 210.27.48.1 and \ (210.27.48.2 or 210.27.48.3 \)
```

- C如果想要获取主机210.27.48.1除了和主机210.27.48.2之外所有主机通信的ip包，使用命令：
```
#tcpdump ip host 210.27.48.1 and ! 210.27.48.2
```

- D如果想要获取主机210.27.48.1接收或发出的telnet包，使用如下命令：
```
#tcpdump tcp port 23 host 210.27.48.1
```

- E 对本机的udp 123 端口进行监视 123 为ntp的服务端口
```
# tcpdump udp port 123
```

- F 系统将只对名为hostname的主机的通信数据包进行监视。主机名可以是本地主机，也可以是网络上的任何一台计算机。下面的命令可以读取主机hostname发送的所有数据：
```
#tcpdump -i eth0 src host hostname
```

- G 下面的命令可以监视所有送到主机hostname的数据包：
```
#tcpdump -i eth0 dst host hostname
```

- H 我们还可以监视通过指定网关的数据包：
```
#tcpdump -i eth0 gateway Gatewayname
```
- I 如果你还想监视编址到指定端口的TCP或UDP数据包，那么执行以下命令：
```
#tcpdump -i eth0 host hostname and port 80
```

- J 如果想要获取主机210.27.48.1除了和主机210.27.48.2之外所有主机通信的ip包，使用命令：
```
#tcpdump ip host 210.27.48.1 and ! 210.27.48.2
```

- K 想要截获主机210.27.48.1 和主机210.27.48.2 或210.27.48.3的通信，使用命令：
```
#tcpdump host 210.27.48.1 and \ (210.27.48.2 or 210.27.48.3 \)
```

- L 如果想要获取主机210.27.48.1除了和主机210.27.48.2之外所有主机通信的ip包，使用命令：
```
#tcpdump ip host 210.27.48.1 and ! 210.27.48.2
```

- M 如果想要获取主机210.27.48.1接收或发出的telnet包，使用如下命令：
```
#tcpdump tcp port 23 host 210.27.48.1
```

### tcpdump的输出结果介绍
下面我们介绍几种典型的tcpdump命令的输出信息

- A,数据链路层头信息
使用命令:
```
#tcpdump --e host ice
```
ice 是一台装有linux的主机，她的MAC地址是0：90：27：58：AF：1A；
H219是一台装有SOLARIC的SUN工作站，它的MAC地址是8：0：20：79：5B：46；
上一条命令的输出结果如下所示：
```
21:50:12.847509 eth0 < 8:0:20:79:5b:46 0:90:27:58:af:1a ip 60: h219.33357 > ice.telne
t 0:0(0) ack 22535 win 8760 (DF)
```
    分析：21：50：12是显示的时间， 847509是ID号，eth0 <表示从网络接口eth0 接受该数据包，eth0>表示从网络接口设备发送数据包, 8:0:20:79:5b:46是主机H219的MAC地址,它表明是从源地址H219发来的数据包.0:90:27:58:af:1a是主机ICE的MAC地址,表示该数据包的目的地址是ICE . ip 是表明该数据包是IP数据包,60是数据包的长度, h219.33357 > ice.telnet表明该数据包是从主机H219的33357端口发往主机ICE的TELNET(23)端口. ack 22535表明对序列号是222535的包进行响应. win 8760表明发送窗口的大小是8760.

- B,ARP包的TCPDUMP输出信息

使用命令:
```
#tcpdump arp
```
得到的输出结果是：
```
22:32:42.802509 eth0 > arp who-has route tell ice (0:90:27:58:af:1a)
22:32:42.802902 eth0 < arp reply route is-at 0:90:27:12:10:66 (0:90:27:58:af:1a)
```

    分析: 22:32:42是时间戳, 802509是ID号, eth0 >表明从主机发出该数据包, arp表明是ARP请求包,who-has route tell ice表明是主机ICE请求主机ROUTE的MAC地址。0:90:27:58:af:1a是主机ICE的MAC地址。

- C,TCP包的输出信息

用TCPDUMP捕获的TCP包的一般输出信息是：
```
src > dst: flags data-seqno ack window urgent options
```
    src> dst:表明从源地址到目的地址, flags是TCP包中的标志信息,S 是SYN标志, F (FIN), P (PUSH) , R(RST) "." (没有标记); data-seqno是数据包中的数据的顺序号, ack是下次期望的顺序号,window是接收缓存的窗口大小, urgent表明数据包中是否有紧急指针. Options是选项.

- D,UDP包的输出信息

用TCPDUMP捕获的UDP包的一般输出信息是：
```
route.port1 > ice.port2: udp lenth
```

    UDP十分简单，上面的输出行表明从主机ROUTE的port1端口发出的一个UDP数据包到主机ICE的port2端口，类型是UDP， 包的长度是lenth

### 辅助工具

- 想查看TCP或者UDP端口使用情况，使用
```
#netstat -anp
```
- 如果有些进程看不见，如只显示”-”，可以尝试
```
#sudo netstat -anp
```

- 如果想看某个端口的信息，使用lsof命令，如：
```
#sudo lsof -i :631
```

```
-bash-3.00# netstat -tln
```

```
netstat -tln #命令是用来查看linux的端口使用情况
```
- 启动ftp端口
```
# /xtc/init.d/vsftp start
```

看文件/etc/services
netstat

查看已经连接的服务端口（ESTABLISHED）

- 查看所有的服务端口（LISTEN，ESTABLISHED）
```
netstat -a
```

- 查看所有 的服务端口并显示对应的服务程序名
```
sudo netstat -ap
```

- nmap ＜扫描类型＞＜扫描参数＞
例如：
```
#nmap localhost
#nmap -p 1024-65535 localhost
#nmap -PT 192.168.1.127-245
```
当我们使用　netstat -apn　查看网络连接的时候，会发现很多类似下面的内容：
```
Proto Recv-Q Send-Q Local Address Foreign Address State PID/Program name

tcp 0 52 218.104.81.152：7710 211.100.39.250：29488 ESTABLISHED 6111/1
```

显示这台服务器开放了7710端口，那么 这个端口属于哪个程序呢？我们可以使用　
```
lsof -i ：7710　
```

命令来查询：
```
COMMAND PID USER FD TYPE DEVICE SIZE NODE NAME

sshd 1990 root 3u IPv4 4836 TCP *：7710 （LISTEN）
```
    这样，我们就知道了7710端口是属于sshd程序的。

- 运行tcpdump命令出现错误信息排除
```
tcpdump: no suitable device found
tcpdump: no devices found /dev/bpf4: A file or directory in the path name does not exist.
```

解决方案 2种原因：
1.权限不够，一般不经过处理，只用root用户能使用tcpdump
2.缺省只能同时使用4个tcpdump，如用完，则报此类错。需要停掉多余的tcpdump

### Ubuntu系统日志配置 /var/log/messages
1. 问题描述
　　今天需要查看Ubuntu系统的日志文件，但却没有找到/var/log/messages这个文件。网上搜素资料，说是要配置/etc/syslog.conf。syslog采用可配置的、统一的系统登记程序，随时从系统各处接受log请求，然后根据/etc/syslog.conf中的预先设定把log信息写入相应文件中、邮寄给特 定用户或者直接以消息的方式发往控制台。

　　好吧，问题又来了。系统中依然没有/etc/syslog.conf，经过一番搜素，最后得到的结论是：在Ubuntu下对应的应该是/etc/rsyslog.conf和rsyslogd。

2. 解决方案
　　关于syslog.conf文件和syslog.d下文件功能解释可以参考下面这篇文章：
　　http://www.linuxidc.com/Linux/2012-03/56434.htm
　　通过查看rsyslog.conf文件，发现所有的配置文件都在/etc/rsyslog.d/文件夹下。

```
# /etc/rsyslog.conf Configuration file for rsyslog.
#
# For more information see
# /usr/share/doc/rsyslog-doc/html/rsyslog_conf.html
#
# Default logging rules can be found in /etc/rsyslog.d/50-default.conf
… …
#
# Include all config files in /etc/rsyslog.d/
#
$IncludeConfig /etc/rsyslog.d/*.conf
```

用vim打开/etc/rsyslog.d/50-default.conf文件增加一行内容如下：

```
*.info;mail.none;authpriv.none;cron.none /var/log/messages
```

[原文][1]

  [1]: http://www.cnblogs.com/yc_sunniwell/archive/2010/07/05/1771563.html