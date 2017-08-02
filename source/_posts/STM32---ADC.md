---
title: STM32---ADC
date: 2017-03-26 01:37:11
tags: [ST,STM32,ADC]
---
- - -
## 引脚
![STM32 ADC 引脚][1]
## 工作流程
1. **ADC通常要与DMA一起使用 这里只是简单的用库配置ADC 不断扫描来实现ADC的应用。**
** 首先配置GPIO与ADC的时钟：**

<!--more-->

```
ADC_InitTypeDef  ADC_InitStructure; 
GPIO_InitTypeDef GPIO_InitStructure;
RCC_APB2PeriphClockCmd(RCC_APB2Periph_ADC1,ENABLE); 
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
GPIO_InitStructure.GPIO_Pin  =GPIO_Pin_1; 
GPIO_InitStructure.GPIO_Mode =GPIO_Mode_AIN; 
GPIO_Init(GPIOB,&GPIO_InitStructure); //默认速度为两兆
```
2 .**配置ADC的运行：**
```
ADC_InitStructure.ADC_Mode = ADC_Mode_Independent;  //独立模式 
ADC_InitStructure.ADC_ScanConvMode      =DISABLE;      //连续多通道模式 
ADC_InitStructure.ADC_ContinuousConvMode =ENABLE;      //连续转换 
ADC_InitStructure.ADC_ExternalTrigConv  = ADC_ExternalTrigConv_None; //转换不受外界决定
 ADC_InitStructure.ADC_DataAlign         =ADC_DataAlign_Right;   //右对齐 
ADC_InitStructure.ADC_NbrOfChannel      =1;       //扫描通道数
 ADC_Init(ADC1,&ADC_InitStructure);
 ADC_RegularChannelConfig(ADC1,ADC_Channel_9, 1,ADC_SampleTime_1Cycles5); //通道X,采样时间为1.5周期,1代表规则通道第1个这个1是啥意思我不太清楚只有是1的时候我的ADC才正常。
 ADC_Cmd  (ADC1,ENABLE);         //使能或者失能指定的ADC
 ADC_SoftwareStartConvCmd(ADC1,ENABLE); //使能或者失能指定的ADC的软件转换启动功能
```
**注意：为了能够正确地配置每一个ADC通道，用户在调用ADC_Init()之后，必须调用ADC_ChannelConfig()来配置每个所使用通道的转换次序和采样时间。**
**然后就是不停的读；**
```
u16 TestAdc(void) 
{ 
u16 adc; 
while(ADC_GetFlagStatus(ADC1, ADC_FLAG_EOC)==RESET); //检查制定ADC标志位置1与否 ADC_FLAG_EOC 转换结束标志位 
adc=ADC_GetConversionValue(ADC1);
returnadc;//返回最近一次ADCx规则组的转换结果
 }
```

[1]:http://upload-images.jianshu.io/upload_images/1736256-98eeb51f671b5b77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240