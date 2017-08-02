---
title: STM32 命名规则
date: 2017-03-26 01:36:11
tags: [ST,STM32]
---
- - -
## 命名规范
以stm32f103c8t7xx为例子

![命名规范](http://upload-images.jianshu.io/upload_images/1736256-7d5b4a61ad2948ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

| Device |  description |
| ------------- |
| STM32 |基于ARM?的32位微控制器产品类型。 |
| F |通用类型产品子系列 |
|Device subfamily|101 = 基本型 102 = USB基本型 103 = 增强型 105/107 = 互联型|
|Pin count| T=36 C=48 R=64 V=100 Z=144| 
|Flash Size|6=32 8=64 B=128 C=256 D=384 E=512 |
|Package|H = BGA T = LQFP U = VFQFPN Y = WLCSP64|

## STM32 产品类型和启动文件的选择
```
- startup_stm32f10x_ld_vl.s: for STM32 Low density Value line devices
- startup_stm32f10x_ld.s: for STM32 Low density devices
- startup_stm32f10x_md_vl.s: for STM32 Medium density Value line devices
- startup_stm32f10x_md.s: for STM32 Medium density devices
- startup_stm32f10x_hd.s: for STM32 High density devices
- startup_stm32f10x_xl.s: for STM32 XL density devices
- startup_stm32f10x_cl.s: for STM32 Connectivity line devices
cl：互联型产品，stm32f105/107系列
vl：超值型产品，stm32f100系列
xl：超高密度产品，stm32f101/103系列
ld：低密度产品，FLASH小于64K
md：中等密度产品，FLASH=64 or 128
hd：高密度产品，FLASH大于128
```
## STM32 Flash 页面大小划分
STM32有4种Flash module organization，分别是：
- low-density devices(32KB，1KB/page)
- medium-density devices(128KB，1KB/page)
- high-density devices(512KB，2KB/page)
- connectivity line devices(256KB，2KB/page)
- XL-density(devices(1M，2KB/page)