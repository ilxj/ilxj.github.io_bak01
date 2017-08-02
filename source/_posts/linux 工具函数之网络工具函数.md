---
title: linux 工具函数之网络工具函数
date: 2017-03-26 01:17:11
tags: [Linux,网络]
---
- - -
**本地IP、子网掩码、网关、MAC地址 获取方法 **
<!--more-->

```c
#include <stdio.h>      
#include <sys/types.h>
#include <ifaddrs.h>
#include <netinet/in.h> 
#include <string.h> 
#include <arpa/inet.h>
#include <net/if.h>
#include <unistd.h>
#include <netdb.h>
#include <sys/socket.h>
#include <linux/sockios.h>

char* getLocalIp( )  
{  
    int sock_get_ip;  
    char ipaddr[50];  
  
    struct   sockaddr_in *sin;  
    struct   ifreq ifr_ip;     
  
    if ((sock_get_ip=socket(AF_INET, SOCK_STREAM, 0)) == -1)  
    {  
         printf("socket create failse...GetLocalIp!\n");  
         return NULL;  
    }  
     
    memset(&ifr_ip, 0, sizeof(ifr_ip));    
    //指定网卡为 eth0 
    strncpy(ifr_ip.ifr_name, "eth0", sizeof(ifr_ip.ifr_name) - 1);     
   
    if( ioctl( sock_get_ip, SIOCGIFADDR, &ifr_ip) < 0 )     
    {     
         return NULL;     
    }       
    sin = (struct sockaddr_in *)&ifr_ip.ifr_addr;     
    strcpy(ipaddr,inet_ntoa(sin->sin_addr));         
    
    printf("local net adapter:[ %-5s ] ip_v4 [ %-20s ]\n", "eth0", ipaddr);    
    close( sock_get_ip );  
      
    return NULL;  
}  
void getlocalIp2()
{
    struct ifaddrs * ifAddrStruct=NULL;  
    void * tmpAddrPtr=NULL;  
  
    getifaddrs(&ifAddrStruct);  
  
   //获取所有网卡的ip
    while (ifAddrStruct!=NULL)   
    {  
        if (ifAddrStruct->ifa_addr->sa_family==AF_INET)  
        {   // check it is IP4  
            // is a valid IP4 Address  
            tmpAddrPtr = &((struct sockaddr_in *)ifAddrStruct->ifa_addr)->sin_addr;  
            char addressBuffer[INET_ADDRSTRLEN];  
            inet_ntop(AF_INET, tmpAddrPtr, addressBuffer, INET_ADDRSTRLEN);  
            printf("local net adapter:[ %-5s ] ip_v4 [ %-20s ]\n", ifAddrStruct->ifa_name, addressBuffer);   
        }  
        else if (ifAddrStruct->ifa_addr->sa_family==AF_INET6)  
        {   // check it is IP6  
            // is a valid IP6 Address  
            tmpAddrPtr=&((struct sockaddr_in *)ifAddrStruct->ifa_addr)->sin_addr;  
            char addressBuffer[INET6_ADDRSTRLEN];  
            inet_ntop(AF_INET6, tmpAddrPtr, addressBuffer, INET6_ADDRSTRLEN);  
            printf("local net adapter:[ %-5s ] ip_v6 [ %-20s ]\n", ifAddrStruct->ifa_name, addressBuffer);   
        }   
        ifAddrStruct = ifAddrStruct->ifa_next;  
    }  
    return 0; 
}
int SetLocalIp( const char *ipaddr )  
{  
  
    int sock_set_ip;  
      
    struct sockaddr_in sin_set_ip;  
    struct ifreq ifr_set_ip;  
  
    bzero( &ifr_set_ip,sizeof(ifr_set_ip));  
   
    if( ipaddr == NULL )  
        return -1;  
  
    if(sock_set_ip = socket( AF_INET, SOCK_STREAM, 0 ) == -1);  
    {  
        perror("socket create failse...SetLocalIp!\n");  
        return -1;  
    }  
   
    memset( &sin_set_ip, 0, sizeof(sin_set_ip));  
    strncpy(ifr_set_ip.ifr_name, "eth0", sizeof(ifr_set_ip.ifr_name)-1);     
      
    sin_set_ip.sin_family = AF_INET;  
    sin_set_ip.sin_addr.s_addr = inet_addr(ipaddr);  
    memcpy( &ifr_set_ip.ifr_addr, &sin_set_ip, sizeof(sin_set_ip));  
  
    if( ioctl( sock_set_ip, SIOCSIFADDR, &ifr_set_ip) < 0 )  
    {  
        perror( "Not setup interface\n");  
        return -1;  
    }  
  
    //设置激活标志  
    ifr_set_ip.ifr_flags |= IFF_UP |IFF_RUNNING;  
  
    //get the status of the device  
    if( ioctl( sock_set_ip, SIOCSIFFLAGS, &ifr_set_ip ) < 0 )  
    {  
         perror("SIOCSIFFLAGS");  
         return -1;  
    }  
  
    close( sock_set_ip );  
    return 0;  
}  
void getLocalNetMask()  
{  
    int sock_netmask;  
    char netmask_addr[50];  
  
    struct ifreq ifr_mask;  
    struct sockaddr_in *net_mask;  
          
    sock_netmask = socket( AF_INET, SOCK_STREAM, 0 );  
    if( sock_netmask == -1)  
    {  
        perror("create socket failture...GetLocalNetMask\n");  
        return ;  
    }  
      
    memset(&ifr_mask, 0, sizeof(ifr_mask));    

    /* 获取指定网卡的子网掩码 */
    strncpy(ifr_mask.ifr_name, "eth0", sizeof(ifr_mask.ifr_name )-1);     
  
    if( (ioctl( sock_netmask, SIOCGIFNETMASK, &ifr_mask ) ) < 0 )   
    {  
        printf("mac ioctl error\n");  
        return ;  
    }  
      
    net_mask = ( struct sockaddr_in * )&( ifr_mask.ifr_netmask );  
    strcpy( netmask_addr, inet_ntoa( net_mask -> sin_addr ) );  
      
    printf("local net adapter:[ %-5s ] mask  [ %-20s ]\n","eth0",netmask_addr);      
      
    close( sock_netmask );  
    return ;  
}
void getLocalMac()  
{  
    int sock_mac;  
      
    struct ifreq ifr_mac;  
    char mac_addr[30];     
      
    sock_mac = socket( AF_INET, SOCK_STREAM, 0 );  
    if( sock_mac == -1)  
    {  
        perror("create socket falise...mac\n");  
        return ;  
    }  
      
    memset(&ifr_mac,0,sizeof(ifr_mac));     
    strncpy(ifr_mac.ifr_name, "eth0", sizeof(ifr_mac.ifr_name)-1);     
  
    if( (ioctl( sock_mac, SIOCGIFHWADDR, &ifr_mac)) < 0)  
    {  
        printf("mac ioctl error\n");  
        return ;  
    }  
      
    sprintf(mac_addr,"%02x%02x%02x%02x%02x%02x",  
            (unsigned char)ifr_mac.ifr_hwaddr.sa_data[0],  
            (unsigned char)ifr_mac.ifr_hwaddr.sa_data[1],  
            (unsigned char)ifr_mac.ifr_hwaddr.sa_data[2],  
            (unsigned char)ifr_mac.ifr_hwaddr.sa_data[3],  
            (unsigned char)ifr_mac.ifr_hwaddr.sa_data[4],  
            (unsigned char)ifr_mac.ifr_hwaddr.sa_data[5]);  
  
    printf("local net adapter:[ %-5s ] mac   [ %-20s ]\n","eth0",mac_addr);      
      
    close( sock_mac );  
    return ;  
}   
void getGateWay()  
{  
    FILE *fp;  
    char buf[512];  
    char cmd[128];  
    char gateway[30];  
    char *tmp;  
  
    strcpy(cmd, "ip route");  
    fp = popen(cmd, "r");  
    if(NULL == fp)  
    {  
        perror("popen error");  
        return ;  
    }  
    while(fgets(buf, sizeof(buf), fp) != NULL)  
    {  
        tmp =buf;  
        while(*tmp && isspace(*tmp))  
            ++ tmp;  
        if(strncmp(tmp, "default", strlen("default")) == 0)  
            break;  
    }  
    sscanf(buf, "%*s%*s%s", gateway);         
    printf("default gateway:%s\n", gateway);  
    pclose(fp);  
      
    return ;  
}  
void main( )
{
    getLocalIp( ) ;
    getlocalIp2( );
    getLocalNetMask();
    getLocalMac();
    getGateWay();
}
```
**运行结果：**
![net.png](http://upload-images.jianshu.io/upload_images/1736256-22fbb52f340934b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)