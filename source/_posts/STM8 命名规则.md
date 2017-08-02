---
title: STM8 命名规则
date: 2017-03-26 01:16:11
tags: [ST,STM8]
---
- - -
**STM8命名规则示列：STM8S005K6T6Cxxx**代表的意义为： 超值型 STM8内核（可以理解为8位51增强型内核）MCU,LQFP-32封装，32KB FLASH容量，温度范围-40℃-85℃；（工业级）
**具体分解如下：**

<!--more-->

|**STM8**|**S**|**005**|**K**|**6**|**T**|**6**|**C**|**XXX**|
|-|-|
|1|2|3|4|5|6|7|8|9

**1.产品系列:**
STM8单片机包括以下几个系列：
STM8： 8位MCU； STM8A：8位自动MCU； STM8T：8位触摸感应MCU； STM8TL：8位触摸感应低电压MCU；
**2.产品类型:**
S：标准型； L：低电压型；
**3.产品子系列：**
005：超值型STM8S005X，速度16MHz； 051：超低压ULTRA串口； 052：带LCD； 007：ARM3超值型，速度24 MHz； 00x：超值型； 003：子系列； 105：基本型STM8S1005X； 10X：基本型； 103：子系列； 207：中间层的外围设定; 208：所有层的外围设定; 903：903子系列; 151：超低压ULTRA串口; 152：超低压ULTRA串口带LCD; 101：子系列；
**4.管脚数**
F：20PIN；G：28PIN；K：32PIN；S：44PIN；C：48PIN；R：64PIN；M：80PIN；
**5.Flash存存容量**
1：2KB flash；（小容量）; 2：4KB flash；（小容量）; 3：8KB flash；（小容量）; 4：16KB flash；（小容量）; 6：32KB flash；（小容量）; 8：64KB flash；（中容量）; B：128KB flash；（中容量）; C：256KB flash；（大容量）; D：384KB flash；（大容量）; E：512KB flash；（大容量）; F：768KB flash；（大容量）; G：1MKB flash；（大容量）
**6.封装**
T：LQFP； P：TSSOP； U：UFQFPN； B：SDIP； M：SO； Y：WLCSP；
**7.温度范围**
3：-40℃-125℃；（工业级）; 6：-40℃-85℃；（工业级）; 7：-40℃-105℃；（工业级）
**8.包装尺寸**
无特性：0.5mm；C：0.8mm; A：0.55mm的UFQFPN；Blank：0.5mm或0.65mm;
低压101系列的A：COMP_REF可变；Blank：COMP_REF不可变;
低压162系列的D：VDD的范围为1.8V-3.6V，且BOR使能； Blank：VDD的范围为1.8V-3.6V，且BOR不使能;
**9.包装方式：**
TR：带卷；无特性：盘装；
- - -
![STM8 MCU 选型列表](http://upload-images.jianshu.io/upload_images/1736256-f177ef8447b808f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![STM8L101F3P6](http://upload-images.jianshu.io/upload_images/1736256-d448c89177176b7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![原理图](http://upload-images.jianshu.io/upload_images/1736256-37063209962e156c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)