---
title: Linux之errno表
date: 2017-06-07 00:33:11
tags: [Linux,工具]
---

# errno 错误表

>errno 是记录系统的最后一次[错误代码](http://baike.baidu.com/item/%E9%94%99%E8%AF%AF%E4%BB%A3%E7%A0%81)。代码是一个int型的值，在errno.h中定义

**注意：**只有当一个[库函数](http://baike.baidu.com/item/%E5%BA%93%E5%87%BD%E6%95%B0)失败时，errno才会被设置。当函数成功运行时，errno的值不会被修改。这意味着我们不能通过测试errno的值来判断是否有错误存在。反之，只有当被调用的函数提示有错误发生时检查errno的值才有意义。

<!--more-->
## 错误表

|错误数| 描述|
|:---:|---|
|errno0 |     Success
|errno1|      Operation not permitted
|errno2|      No such file or directory
|errno3|      No such process
|errno4 |     Interrupted system call
|errno5 |     Input/output error
|errno6 |     No such device or address
|errno7 |     Argument list too long
|errno8 |     Exec format error
|errno9 |     Bad file descriptor
|errno10 |    No child processe
|errno11 |    Resource temporarily unavailable
|errno12 |    Cannot allocate memory
|errno13|     Permission denied
|errno14 |    Bad address
|errno15 |    Block device required
|errno16|     Device or resource busy
|errno17 |    File exists
|errno18|     Invalid cross-device link
|errno19|     No such device
|errno20 |    Not a directory
|errno21 |    Is a directory
|errno22|     Invalid argument
|errno23 |    Too many open files in system
|errno24 |    Too many open files
|errno25 |    Inappropriate ioctl for device
|errno26 |    Text file busy
|errno27|     File too large
|errno28|     No space left on device
|errno29 |    Illegal seek
|errno30 |    Read-only file system
|errno31|     Too many links
|errno32|     Broken pipe
|errno33|     Numerical argument out of domain
|errno34 |    Numerical result out of range
|errno35|     Resource deadlock avoided
|errno36|     File name too long
|errno37|     No locks available
|errno38 |    Function not implemented
|errno39 |    Directory not empty
|errno40|     Too many levels of symbolic links
|errno41|     Unknown error 41
|errno42|     No message of desired type
|errno43|     Identifier removed
|errno44 |    Channel number out of range
|errno45 |    Level 2 not synchronized
|errno46 |    Level 3 halted
|errno47|     Level 3 reset
|errno48 |    Link number out of range
|errno49|     Protocol driver not attached
|errno50 |    No CSI structure available
|errno51|     Level 2 halted
|errno52|    Invalid exchange
|errno53|     Invalid request descriptor
|errno54|     Exchange full
|errno55 |    No anode
|errno56 |    Invalid request code
|errno57 |    Invalid slot
|errno58 |    Unknown error 58
|errno59 |    Bad font file format
|errno60 |    Device not a stream
|errno61 |    No data available
|errno62 |    Timer expired
|errno63|     Out of streams resources
|errno64 |    Machine is not on the network
|errno65 |    Package not installed
|errno66|     Object is remote
|errno67 |    Link has been severed
|errno68|     Advertise error
|errno69 |    Srmount error
|errno70 |    Communication error on send
|errno71|     Protocol error
|errno72|     Multihop attempted
|errno73|     RFS specific error
|errno74|     Bad message
|errno75|     Value too large for defined datatype
|errno76|     Name not unique on network
|errno77|     File descriptor in bad state
|errno78|     Remote address changed
|errno79 |    Can not access a needed sharedlibrary
|errno80 |    Accessing a corrupted sharedlibrary
|errno81|     .lib section in a.out corrupted
|errno82|     Attempting to link in too manyshared libraries
|errno83|     Cannot exec a shared librarydirectly
|errno84 |    Invalid or incomplete multibyte orwide character
|errno85 |    Interrupted system call should berestarted
|errno86 |    Streams pipe error
|errno87|     Too many users
|errno88 |    Socket operation on non-socket
|errno89|     Destinationaddress required
|errno90 |    Message too long
|errno91|     Protocol wrong type for socket
|errno92|     Protocol not available
|errno93 |    Protocol not supported
|errno94|     Socket type not supported
|errno95|     Operation not supported
|errno96|     Protocol family not supported
|errno97|     Address family not supported byprotocol
|errno98 |    Address already in use
|errno99|     Cannot assign requested address
|errno100|    Network is down
|errno101|    Network is unreachable
|errno102|    Network dropped connection onreset
|errno103|    Software caused connection abort
|errno104|    Connection reset by peer
|errno105|    No buffer space available
|errno106|    Transport endpoint is alreadyconnected
|errno107|    Transport endpoint is notconnected
|errno108 |   Cannot send after transportendpoint shutdown
|errno109|    Too many references: cannot splice
|errno110|    Connection timed out
|errno111|    Connection refused
|errno112 |   Host is down
|errno113 |   No route to host
|errno114 |   Operation already in progress
|errno115|    Operation now in progress
|errno116 |   Stale NFS file handle
|errno117 |   Structure needs cleaning
|errno118|    Not a XENIX named type file
|errno119|    No XENIX semaphores available
|errno120 |   Is a named type file
|errno121 |   Remote I/O error
|errno122|    Disk quota exceeded
|errno123|    No medium found
|errno124|    Wrong medium type
|errno125 |   Operation canceled
|errno126 |   Required key not available
|errno127|    Key has expired
|errno128|    Key has been revoked
|errno129|    Key was rejected by service
|errno130|    Owner died
|errno131 |   State not recoverable
|errno132|    Operation not possible due toRF-kill
|errno133 |   Unknown error 133
|errno134 |   Unknown error 134
|errno135|    Unknown error 135
|errno136|    Unknown error 136
|errno137|    Unknown error 137
|errno138 |   Unknown error 138
|errno139 |   Unknown error 139