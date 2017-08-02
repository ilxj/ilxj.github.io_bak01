---
title: Linux之Socket关键
date: 2017-06-07 23:10:11
tags: [Linux,网络]
---

## 一、Socket 属性设置函数

```
int getsockopt(int sockfd, int level, int optname,void *optval, socklen_t *optlen);
int setsockopt(int sockfd, int level, int optname,const void *optval, socklen_t optlen);
```
解释:**setsockopt(int sockfd, int level, int optname,const void *optval, socklen_t optlen)**
<!--more-->
- **sockfd(套接字):** 指向一个打开的套接口描述字
- **level(级别):** 指定选项代码的类型
- - **SOL_SOCKET:** 基本套接口
- - **IPPROTO_IP:** IPv4套接口
- - **IPPROTO_IPV6:** IPv6套接口
- - **IPPROTO_TCP:** TCP套接口
- **optname(选项名):** 选项名称
- **optval(选项值):** 是一个指向变量的指针 类型：整形，套接口结构， 其他结构类型:linger{}, timeval{ }
该值有两种类型，分别是布尔型选项，允许或禁止一种特性；另一种是整形或结构选项。
允许一个布尔型选项，则将optval指向非零整形数；
禁止一个选项optval指向一个等于零的整形数。对于布尔型选项，optlen应等于sizeof(int)；
对其他选项，optval指向包含所需选项的整形数或结构，而optlen则为整形数或结构的长度。
- **optlen(选项长度):** optval 的大小


## 二、Socket 端口复用(SO_REUSEADDR)

> 一个网络应用程序只能绑定一个端口( 一个套接字只能绑定一个端口 )。

1. *端口复用允许在一个应用程序可以把 n 个套接字绑在一个端口上而不出错。*
2. *端口复用真正的用处主要在于服务器编程：当服务器需要重启时，经常会碰到端口尚未完全关闭的情况，这时如果不设置端口复用，则无法完成绑定，因为端口还处于被别的套接口绑定的状态之中。*

### 使用场景
>**SO_REUSEADDR可以用在以下四种情况下。 (摘自《Unix网络编程》卷一，即UNPv1)**

1. 当有一个有相同本地地址和端口的socket1处于TIME_WAIT状态时，而你启动的程序的socket2要占用该地址和端口，你的程序就要用到该选项。
2. SO_REUSEADDR允许同一port上启动同一服务器的多个实例(多个进程)。但每个实例绑定的IP地址是不能相同的。在有多块网卡或用IP Alias技术的机器可以测试这种情况。
3. SO_REUSEADDR允许单个进程绑定相同的端口到多个socket上，但每个socket绑定的ip地址不同。这和2很相似，区别请看UNPv1。
4. SO_REUSEADDR允许完全相同的地址和端口的重复绑定。但这只用于UDP的多播，不用于TCP。

### 实现方法

```
int opt = 1;
// sockfd为需要端口复用的套接字
setsockopt(sockfd, SOL_SOCKET, SO_REUSEADDR, (const void *)&opt, sizeof(opt));
```

## 三、Socket 超时设置(SO_SNDTIMO/SO_RCVTIMEO)
**SO_SNDTIMO :** 用于设置发送和连接超时
**SO_RCVTIMEO:** 用于设置接收超时
**例 子**
```
//3s超时设置
struct timeval timeout = {3, 0};
//socket设置发送超时
setsockopt(socket，SOL_SOCKET,SO_SNDTIMEO，(char *)&timeout,sizeof(struct timeval));

//socket设置接收超时
setsockopt(socket，SOL_SOCKET,SO_RCVTIMEO，(char *)&timeout,sizeof(struct timeval));

//socket设置连接超时
setsockopt(fd, SOL_SOCKET, SO_SNDTIMEO, &timeo, len);
addr.sin_family = AF_INET;
addr.sin_addr.s_addr = inet_addr(argv[1]);
addr.sin_port = htons(atoi(argv[2]));
if (connect(fd, (struct sockaddr*)&addr, sizeof(addr)) == -1) {
    if (errno == EINPROGRESS) {
            fprintf(stderr, "timeout/n");
            return -1;
    }
    perror("connect");
    return 0;
}
```
## 四、Socket 心跳设置(SO_KEEPALIVE)
>在《UNIX网络编程第1卷》中也有详细的阐述：
SO_KEEPALIVE 保持连接检测对方主机是否崩溃，避免（服务器）永远阻塞于TCP连接的输入。设置该选项后，如果2小时内在此套接口的任一方向都没有数据交换，TCP就自 动给对方 发一个保持存活探测分节(keepalive probe)。这是一个对方必须响应的TCP分节.它会导致以下三种情况：对方接收一切正常：以期望的ACK响应。2小时后，TCP将发出另一个探测分 节。对方已崩溃且已重新启动：以RST响应。套接口的待处理错误被置为ECONNRESET，套接 口本身则被关闭。对方无任何响应：源自berkeley的TCP发送另外8个探测分节，相隔75秒一个，试图得到一个响应。在发出第一个探测分节11分钟 15秒后若仍无响应就放弃。套接口的待处理错误被置为ETIMEOUT，套接口本身则被关闭。如ICMP错误是“host unreachable(主机不可达)”，说明对方主机并没有崩溃，但是不可达，这种情况下待处理错误被置为 EHOSTUNREACH。

**简单来说**
*TCP_KEEPALIVE*选项只是一个开关，Linux中默认的Keepalive的选项如下：
```
$sudo sysctl -a | grep keepalive
net.ipv4.tcp_keepalive_time = 7200
net.ipv4.tcp_keepalive_probes = 9
net.ipv4.tcp_keepalive_intvl = 75
```

上文中的keepalive选项表示如果一个连接上7200s后没有任何数据发送，则设置了这个选项的本端向对端发送keepalive保活报文，它会有如下三种结果：
- 对端回复ACK。则本端TCP认为该连接依然存活。继续等7200s后再发送keepalive报文。
- 对端回复RESET。说明对端进程已经重启，本端的应用程序应该关闭该连接。
- 没有对端的任何回复。则本端做重试，如果重试9次（前后重试间隔为75秒）仍然不可达，则向应用程序返回错误信息，ETIMEOUT（无任何应答）或EHOST

### 开启keepalive机制
```
keepAlive = 1;
Setsockopt(listenfd, SOL_SOCKET, SO_KEEPALIVE, (void*)&keepAlive, sizeof(keepAlive));
```
### 修改过keepalive 时间
如果我们不能接受如此之长的等待时间，从TCP-Keepalive-HOWTO上可以知道一共有两种方式可以设置，
一种是修改内核关于网络方面的 配置参数；
另外一种就是SOL_TCP字段的TCP_KEEPIDLE， TCP_KEEPINTVL， TCP_KEEPCNT三个选项。
- TCP_KEEPIDLE : 多长时间不活跃，就开始发探测包
- TCP_KEEPINTVL: 两个探测包之间的间隔
- TCP_KEEPCNT  : 发多少次探测包后没响应认为是断开
**例子**
```
//连接上之后100s内没发送数据，则发探测包，每个探测包间隔10s,连续10次没收到响应认为断开
int                 keepIdle = 100;
int                 keepInterval = 10;
int                 keepCount = 10;
Setsockopt(listenfd, SOL_TCP, TCP_KEEPIDLE, (void *)&keepIdle, sizeof(keepIdle));
Setsockopt(listenfd, SOL_TCP, TCP_KEEPINTVL, (void *)&keepInterval, sizeof(keepInterval));
Setsockopt(listenfd, SOL_TCP, TCP_KEEPCNT, (void *)&keepCount, sizeof(keepCount));
```


## 五、Socket 其他选项名
- SO_RCVBUF             : int型  设置接收缓冲区的保留大小。
- SO_SNDBUF             : int型  设置发送缓冲区的保留大小。
- TCP_NODELAY           : bool型 设置是否开启Nagle算法。
- SO_EXCLUSIVEADDRUSE   : bool型 同一个端口不能别的进程使用.


## 六、常识

- 每个套接口都有一个发送缓冲区和一个接收缓冲区。
- 接收缓冲区被TCP和UDP用来将接收到的数据一直保存到由应用进程来读。
- TCP：TCP通告另一端的窗口大小。
- TCP套接口接收缓冲区不可能溢出，因为对方不允许发出超过所通告窗口大小的数据。这就是TCP的流量控制，如果对方无视窗口大小而发出了超过宙口大小的数据，则接 收方TCP将丢弃它。
- UDP：当接收到的数据报装不进套接口接收缓冲区时，此数据报就被丢弃。UDP是没有 流量控制的；快的发送者可以很容易地就淹没慢的接收者，导致接收方的UDP丢弃数据报。

每个套接口都有一个接收低潮限度和一个发送低潮限度。
它们是函数selectt使用的，接收低潮限度是让select返回“可读”而在套接口接收缓冲区中必须有的数据总量。 对于一个TCP或UDP套接口，此值缺省为1。发送低潮限度是让select返回“可写” 而在套接口发送缓冲区中必须有的可用空间。对于TCP套接口，此值常缺省为2048。 对于UDP使用低潮限度， 由于其发送缓冲区中可用空间的字节数是从不变化的，只要 UDP套接口发送缓冲区大小大于套接口的低潮限度，这样的UDP套接口就总是可写的。 UDP没有发送缓冲区，只有发送缓冲区的大小。
