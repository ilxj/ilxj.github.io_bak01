---
title: WireShark网络抓包
date: 2017-07-01 19:19:11
tags: [抓包工具,wireShark]
categories: 工具
---

---
## WireShark入门
### 一、 打开WireShark
![WireShark启动界面][1]

<!--more-->
### 二、界面分区
![WireShark界面划分][2]
### 三、过滤规则语句

-  IP 过滤
```
ip.src eq 192.168.1.107 or ip.dst eq 192.168.1.107
#或者
ip.addr eq 192.168.1.107 // 都能显示来源IP和目标IP
```

-  过滤端口
```
tcp.port eq 80 // 不管端口是来源的还是目标的都显示
tcp.port == 80
tcp.port eq 2722
tcp.port eq 80 or udp.port eq 80
tcp.dstport == 80 // 只显tcp协议的目标端口80
tcp.srcport == 80 // 只显tcp协议的来源端口80
#端口范围
tcp.port >= 1 and tcp.port <= 80
```
- 协议过滤
```
tcp、udp、arp、icmp、http、smtp、ftp、dns、msnms、ip、ssl、oicq、bootp #等等.
#排除
#排除arp包
!arp  或者  not arp
```
- mac 过滤
```
eth.dst == A0:00:00:04:C5:84 //过滤目标mac
eth.src eq A0:00:00:04:C5:84 //过滤来源mac
eth.dst==A0:00:00:04:C5:84
eth.dst==A0-00-00-04-C5-84
eth.addr eq A0:00:00:04:C5:84 //过滤来源MAC和目标MAC都等于A0:00:00:04:C5:84的MAC
```
- 数据包长度过滤
```
udp.length == 26 #这个长度是指udp本身固定长度8加上udp下面那块数据包之和
tcp.len >= 7  #指的是ip数据包(tcp下面那块数据),不包括tcp本身
ip.len == 94 #除了以太网头固定长度14,其它都算是ip.len,即从ip本身到最后
frame.len == 119 #整个数据包长度,从eth开始到最后
#数据包组成成员
eth ---> ip or arp ---> tcp or udp ---> data
```

- http模式过滤
```
http.request.method == "GET"
http.request.method == "POST"
http.request.uri == "/img/logo-edu.gif"
http contains "GET"
http contains "HTTP/1."

// GET包
http.request.method == "GET" && http contains "Host: "
http.request.method == "GET" && http contains "User-Agent: "
// POST包
http.request.method == "POST" && http contains "Host: "
http.request.method == "POST" && http contains "User-Agent: "
// 响应包
http contains "HTTP/1.1 200 OK" && http contains "Content-Type: "
http contains "HTTP/1.0 200 OK" && http contains "Content-Type: "
```
- 两种条件成立过滤
```
过滤两种条件时，使用and连接，
如过滤ip为192.168.101.8并且为http协议的，ip.src==192.168.101.8 and http。
```
- 域名过滤
```
http.host == "gizwits.com"
```
### 四、TCP包分析

#### **一个TCP包由5部分组成分别是:**

1. Frame:   物理层的数据帧概况
2. Ethernet II: 数据链路层以太网帧头部信息
3. Internet Protocol Version 4: 互联网层IP包头部信息
4. Transmission Control Protocol:  传输层T的数据段头部信息，此处是mqtt
5. Hypertext Transfer Protocol/Data:  应用层的信息，此处是mqtt协议

#### **与之对应的网络七层协议图如下**
![tcp数据层][3]
#### OSI 参考模型与 TCP/IP分层模型
![OSI 参考模型与 TCP/IP分层模型][5]

#### **TCP 三次握手**
**图片版本**
![TCP三次握手][6]
#### **WireShark抓包分析图**
##### **第一次握手**
代表客户端请求建立连接
```
SEQ=0
ACK=0
FLAGS = 0x0002 （SYN）
```
**WireShark第一次TCP握手图**
![TCP 第一次握手][7]

##### **第二次握手：**
服务器发回确认包, 标志位为 SYN,ACK. 将确认序号(Acknowledgement Number)设置为客户的SEQ加1以.即ACK=SEQ+1
```
SEQ = 0
ACK = SEQ+1
FLAGS = 0X0012（SYN +ACK）
```
**WireShark第二次TCP握手图**
![TCP 第二次握手][8]


##### **第三次握手**
客户端再次发送确认包(ACK) SYN标志位为0,ACK标志位为1.并且把服务器发来ACK的序号字段+1,放确在SEQ字段中发送给对方.
```
SEQ =SEQ+1
ACK = 1;
FLAGS = 0X0001（ACK）
```
![TCP 第三次握手][9]

### 五、WireShark 抓包异常情况分析

#### 1. **tcp out-of-order**（tcp有问题）
>**原因:** 应该有很多原因。但是多半是网络拥塞，导致顺序包抵达时间不同，延时太长，或者包丢失，需要重新组合数据单元 因为他们可能是通过不同的路径到达你电脑上面的。

#### 2. **tcp segment of a reassembled PDU**
>**原因:** 在连个连接建立的时候，SYN包里面会把彼此TCP最大的报文段长度，在局域网内一般都是**1460**.如果发送的包比最大的报文段长度长的话就要分片了，被分片出来的包，就会被标记了**“TCP segment of a reassembled PDU”**。TCP在发起连接的第一个报文的TCP头里通过MSS这个可选项告知对方本端能够接收的最大报文（当然，这个大小是TCP净荷的大小），以太网上这个值一般设置成1460，因为1460Byte净荷+20Byte TCP头+20Byte IP头 ＝ 1500字节，正好符合链路层最大报文的要求，被标记了的包的SEQ和ACK都和原来的包一致。
#### 3. **Tcp previous segment lost（tcp先前的分片丢失）**
>一般是网络中的网络设备发生的拥塞了，触发了TCP的拥塞控制机制。它告诉发送方数据段丢失；
（1）、“TCP Previous segment lost” errors are not “fatal” errors. They simply indicate that the sequence number in the arriving packet is higher than the next-expected sequence number, indicating that at least one segment was dropped/lost. The receiving station remedies this situation by sending duplicate ACKs for each additional packet it receives until the sender retransmits the missing packet(s). TCP is designed to recover from this situation, which is why the image is downloaded correctly despite having a (briefly) missing packet.
If you are getting a large number of lost packets, then there is likely a communication problem between the sender and receiver. A common cause of this is un-matched duplex settings between the PC and the switch.
We (our lab) recently upgraded to Ethereal 0.10.14 with WinPCap 3.1.  If I remember correctly, we had previously been using 0.10.2 with WinPCap 3.0.  However, since the upgrade we have been noticing several issues.
The first issue is with “TCP Previous segment lost” and “TCP CHECKSUM INCORRECT” messages appearing in the Packet Listing window.  We do not remember seeing these in the previous version of Ethereal, or at least not nearly as many as we are seeing now.  For example, one task for the student instructional part of the lab involves visiting a website containing two images and observing the network activity.  After the two GET requests are sent for the images, it is not uncommon for one image to be returned with a typical 200 OK response packet, but the response packet for the other image will be displayed as “TCP Previous segment lost.”  However, both images are downloaded and displayed perfectly fine in the browser.  I would think that the segment lost error would mean the object wasn’t returned correctly and shouldn’t be able to be displayed, but apparently that is not the case.  (The cache had been cleared when this was performed, so it was not defaulting to a local copy of the image.)
Another problem we’ve been noticing is that some packets simply aren’t displayed in the Packet Listing window, even when they are obviously received.  Using the same example as above, after the two GET requests are sent for the images, it is not uncommon for one image to be returned with a typical 200 OK response, but the other response will not appear.  Yet both images are successfully displayed in the browser.  Is this a problem with Ethereal not detecting the packets?
I’m not sure how typical this is, but we seem to be experiencing these issues often with 0.10.14 while we never did with 0.10.2.  Could it also be an issue with WinPCap, and not necessarily Ethereal?  I’m just trying to find some answers as to why we are seeing a sudden abundance of TCP related errors and uncaptured packets.  Thanks.
（2）、I have a network client application that runs fine while I am debugging (no TCP errors),
but when I run the release version, it runs incredibly slow.  It runs as a series of
transactions, where each transaction is a separate connection to the server.  Wireshark
analysis has determined that about 50% of all transactions involve the series:
TCP Previous Segment Lost

#### 4. Tcpacked lost segment（tcp应答丢失）
#### 5. Tcp window update（tcp窗口更新）
#### 6. Tcp dup ack（tcp重复应答）
>1)当收到tcp out-of-order 信号时，可能发送该信号， 这个相同应答的意图是让对端知道一个分片被收到的时候出现问题，并且告诉它希望得到的序列号。
2)由于TCP不知道重复的ACK是由丢失的段引起的还是仅仅是重新排序段，所以等待接收到少量的重复的ACK。 假设如果只有段的重新排序，则在重新排序的段被处理之前将只有一个或两个重复的ACK，然后将生成新的ACK。 如果在一行中接收到三个或更多个重复的ACK，则表明段已经丢失。 TCP然后执行似乎是丢失段的重传，而不等待重传定时器到期。

#### 7. **Tcp keep alive**（tcp保持活动）
>在TCP中有一个Keep-alive的机制可以检测死连接，原理很简单，TCP会在空闲了一定时间后发送数据给对方：
 1.如果主机可达，对方就会响应ACK应答，就认为是存活的。
 2.如果可达，但应用程序退出，对方就发RST应答，发送TCP撤消连接。
 3.如果可达，但应用程序崩溃，对方就发FIN消息。
 4.如果对方主机不响应ack, rst，继续发送直到超时，就撤消连接。这个时间就是默认的二个小时。

#### 8. **Tcp retransmission**（tcp重传）
>作为一个可靠的传输协议，传输控制协议（TCP）在发送主机需要从目标主机收到一个包时确认。If the sender does not receive that acknowledgment within a certain amount of time, it acts under the assumption that the packet did not reach its destination and retransmits the packet.如果发件人没有收到的时间内一定之金额，确认，它的行为假设下，该数据包没有到达其目的地，以及转发数据包。


## TCP 状态分析
TCP的状态 (SYN, FIN, ACK, PSH, RST, URG)
在TCP层，有个FLAGS字段，这个字段有以下几个标识：SYN, FIN, ACK, PSH, RST, URG.
它们的含义是：

 1. SYN(synchronous): 表示建立连接.
 2. FIN(finish): 表示关闭连接.
 3. ACK(acknowledgement): 表示响应.
 4. PSH(push): 表示有DATA数据传输.
 5. RST(reset): 表示连接重置.
 6. URG(urgent):紧急.
 7. SEQ(Sequence number):顺序号码

## TCP 三次握手流程
1. 主机A发送位码为syn＝1，随机产生seq number=1234567的数据包到服务器，主机B由SYN=1知道，A要求建立联机；
2. 主机B收到请求后要确认联机信息，向A发送ack number=(主机A的seq+1)，syn=1，ack=1，随机产生seq=7654321的包；
3. 主机A收到后检查ack number是否正确，即第一次发送的seq number+1，以及位码ack是否为1，若正确，主机A会再发送ack number=(主机B的seq+1)，ack=1，主机B收到后确认seq值与ack=1则连接建立成功。

## 各种网络包头格式
### 以太网头
```
// MAC地址长度
#define MAC_ADDRESS_LEN 6

// 常用的以太网帧类型
#define ETHERNET_FRAME_IP       0x0800              // IP帧
#define ETHERNET_FRAME_ARP      0x0806              // ARP帧
#define ETHERNET_FRAME_RARP     0x8035              // RARP帧
#define ETHERNET_FRAME_IPv6     0x86DD              // IP6帧

typedef struct _EthernetHeader      // 以太头
{
    UCHAR       DstMacAddr[MAC_ADDRESS_LEN];        // 目标MAC
    UCHAR       SrcMacAddr[MAC_ADDRESS_LEN];        // 源MAC
    USHORT      FrmType;                            // 类型
}EthernetHeader, *PEthernetHeader;
```
### TCP 包格式
```
0                            15                              31
-----------------------------------------------------------------
|          source port          |       destination port        |
-----------------------------------------------------------------
|                        sequence number                        |
-----------------------------------------------------------------
|                     acknowledgment number                     |
-----------------------------------------------------------------
|  HL   | rsvd  |C|E|U|A|P|R|S|F|        window size            |
-----------------------------------------------------------------
|         TCP checksum          |       urgent pointer          |
-----------------------------------------------------------------
|                           Optional                            |
-----------------------------------------------------------------
|                              Data                             |
-----------------------------------------------------------------

// TCP头标志位掩码 OffFlag
#define TCP_HDR_LEN             0xF000              // TCP头长度<双字的个数>转化为字节数需要x4
#define TCP_URG_FLAG            0x0020              // URG标志
#define TCP_ACK_FLAG            0x0010              // ACK标志 应答1或请求0
#define TCP_PSH_FLAG            0x0008              // psh标志 以最快速度传输数据
#define TCP_RST_FLAG            0x0004              // RST标志 先断开连接,再重建连接
#define TCP_SYN_FLAG            0x0002              // SYN标志 用来建立连接
#define TCP_FIN_FLAG            0x0001              // FIN标志 发送方完成数据发送

typedef struct _TcpHeader           // TCP头 20 BYTES
{
    USHORT      SrcPort;                            // 源端口
    USHORT      DstPort;                            // 目的端口
    ULONG       SeqNum;                             // 序号
    ULONG       AckNum;                             // 确认号
    USHORT      OffFlag;                            // 首部长度<首部双字的个数>+保留位+标志位
    USHORT      WndSize;                            // 窗口大小<实现流量控制>
    USHORT      Checksum;                           // 效验和
    USHORT      UrgPointer;                         // 紧急指针
}TcpHeader, *PTcpHeader;

// OffFlag : 4位首部长度 + 6位保留位 + URG+ACK+PSH+RST+SYN+FIN

typedef struct _TCPPACKET           // TCP封包 <当ip中没有附加数据时>
{
    EthernetHeader  Ehhdr;
    IPHeader        Iphdr;
    TcpHeader       Tcphdr;
}TCPPACKET, *PTCPPACKET;
```

SYN和ACK可能同时为1，它表示的就是建立连接之后的响应，
如果只是单个的一个SYN，它表示的只是建立连接。



参考
- - -
- http://bbs.pediy.com/thread-147644.htm
- http://www.yunsec.net/a/school/wlcs/agreement/2012/0317/10262.html
- http://www.9upk.com/article/2537.html （抓包工具教程）


  [1]: http://static.zybuluo.com/AlexLin/4224884og6inde3h2q5wcw6a/image.png
  [2]: http://static.zybuluo.com/AlexLin/zrfd5f3v3q3o8bjdjay8oxc1/image.png
  [3]: http://static.zybuluo.com/AlexLin/9i2ohkrz9yuzz9r9bl2e03uy/image.png
  [4]: http://static.zybuluo.com/AlexLin/hfo94lx43waw32h7modimump/image.png
  [5]: http://static.zybuluo.com/AlexLin/kyugitt8dt3gmr6giuztsm6d/image.png
  [6]: http://static.zybuluo.com/AlexLin/5agc64rl32b44qhqvbj533ug/image.png
  [7]: http://static.zybuluo.com/AlexLin/3eqzyyj3av3lr9a6kafu9xnx/image.png
  [8]: http://static.zybuluo.com/AlexLin/sahnt2ey9phdwwclu1q4hnke/image.png
  [9]: http://static.zybuluo.com/AlexLin/gzzy5f4qh90wvu1992k61on7/image.png