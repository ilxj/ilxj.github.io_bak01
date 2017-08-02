---
title: Tool 记
date: 2017-03-26 00:40:11
tags: [WiFi,esp8266,esptool]
---
- - -
# 工具1--esptool

## 安装方法:
1.超级简单按照方法
``` bash 
 $  pip install esptool
```
2.源码安装方法.
``` bash 
 # 下载源码
 $ git@github.com:themadinventor/esptool.git
 #安装依赖
 $ pip install pyserial, easy_install pyserial 
 #或者 depending on your platform. 
 $ apt-get install python-serial
 ```
<!--more-->

## 帮助说明
```
esptool.py write_flash -h
```

## 使用例子:
```
sudo python esptool.py --port /dev/ttyUSB0 --baud 1500000 \
write_flash -fm qio -fs 32m -ff 40m 0x3fc000 ../bin/0x3fc000.bin
```
## 几个文件一起烧录
```
sudo esptool.py --port /dev/tty.usbserial-DA00H0QE \
--baud 1500000 write_flash -fm qio -fs 32m -ff 40m 0x00000 \
../boot_v1.6.bin 0x01000 user1.4096.new.6.bin 0x3fc000 \
../esp_init_data_default.bin 0x3fe000 ../blank.bin 
```
### 解释
|关键字| 解释|
|----|
|**port**|                 指定串口号|
|**baud**|               指定下载波特率
|**write_flash**|      烧写二进制文件到指定位置
|**-fm**|                  SPI Flash mode( 'qio', 'qout', 'dio', 'dout')     
|**-ff**|                    SPI Flash frequency('40m', '26m', '20m', '80m')
|**-fs**|                   SPI Flash size in Mbit(4m', '2m', '8m', '16m', '32m', '16m-c1', '32m-c1', '32m-c2', '64m', '128m')
|**0x3fc000**|         指定烧录地址
|**0x3fc000.bin**|   对应的烧录文件