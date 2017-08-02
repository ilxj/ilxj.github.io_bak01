---
title: Linux之实用技能
date: 2017-06-06 22:29:11
tags: [Linux,工具]
---

## 查看机器是多少位
- - -
### 方法一
```
>>>$ getconf LONG_BIT
>>>$ 64
```
### 方法二
```
>>>$ uname -a
>>>$ Linux alex-virtual-machine 3.16.0-30-generic #40~14.04.1-Ubuntu SMP Thu Jan 15 17:43:14 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux

```

<!--more-->

### 方法三
```
>>>$ uname -p
>>>$ x86_64
```
## 实用命令
|命令|解释|
|:---:|---|
|wc |统计文本中行数、字数、字符数|
|stat           |显示指定文件的详细信息，比ls更详细
|who          |显示在线登陆用户
|whoami    |显示当前操作用户
|hostname |显示主机名
|uname      |显示系统信息
|top            |动态显示当前耗费资源最多进程信息
|ps             |显示瞬间进程状态 ps -aux
|du             |查看目录大小 du -h /home带有单位显示目录信息
|df              |查看磁盘大小 df -h 带有单位显示磁盘信息

## Linux 代码文件大小、行数、字节数统计

### 命令解释
***语法：wc [选项] 文件…***

>说明：该命令统计给定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc同时也给出所有指定文件的总统计数。字是由空格字符区分开的最大字符串。

该命令各选项含义如下：
- \- c 统计字节数。
- \- l 统计行数。
- \- w 统计字数。

这些选项可以组合使用。

输出列的顺序和数目不受选项的顺序和数目的影响。

总是按下述顺序显示并且每项最多一列。

行数、字数、字节数、文件名

如果命令行中没有文件名，则输出中不出现文件名。

### 例子
#### 查看某个文件
```shell
Alex@alex:blog(master *)$wc -lcw package.json
      24      44     618 package.json
```
#### 查看某目录下的所有文件(包括子目录)
##### 查看blog 目录下命名后缀是html 文件所有的数量
```shell
Alex@alex:blog$find blog/ -name "*.html"|wc -l
     199
```
#### 目录下所有md文件代码行数
```
find blog/ -name "*.md" |xargs cat|wc -l
```

或
```
 wc -l `find ./ -name "*.md"`|tail -n1
```
#### 统计当前目录下文件个数
```
ls -l |grep "^-"|wc -l
```
#### 统计当前文件夹下目录的个数
```
ls -l |grep "^d"|wc -l
```
#### 统计当前文件夹下文件的个数，包括子文件夹里的
```
ls -lR|grep "^-"|wc -l
```
#### 统计文件夹下目录的个数，包括子文件夹里的
```
ls -lR|grep "^d"|wc -l
```


## 性能分析工具 TOP命令
>TOP命令是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况

**命令格式：**
top [-] [d] [p] [q] [c] [C] [S]    [n]

**查看某个用户下的所有进程**
```
alex@alex-virtual-machine:/mnt/hgfs/gagent/working/demo$ top -u alex

PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
2763 alex      20   0 80420  16m  10m S   10  1.6   0:11.69 unity-panel-ser
2865 alex      20   0 62216 4160 3268 S    6  0.4   0:05.54 hud-service
2617 alex      20   0  4544 2144  632 S    4  0.2   0:05.30 dbus-daemon
2646 alex      20   0  134m  12m 9884 S    2  1.2   0:09.35 metacity
2667 alex      20   0  107m  22m  17m S    2  2.3   0:09.72 unity-2d-panel
2679 alex      20   0 49608 8884 6936 S    2  0.9   0:02.26 bamfdaemon
2681 alex      20   0  104m  13m  10m S    2  1.4   0:02.65 nm-apple
```
**查看单独某个进程**
````
alex@alex-virtual-machine:/mnt/hgfs/gagent/working/demo$ ps -a
  PID TTY          TIME CMD
 9463 pts/1    00:00:00 gizwits
 9471 pts/2    00:00:00 ps
alex@alex-virtual-machine:/mnt/hgfs/gagent/working/demo$ top -p 9463
PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
9463 alex      20   0 30424 1152  944 S    2  0.1   0:00.74 gizwits
````
**说明**

|名称|解释|
|:---:|---|
|PID|进程的ID
|USER|进程所有者
|PR|进程的优先级别，越小越优先被执行
|NInice|值
|VIRT|进程占用的虚拟内存
|RES|进程占用的物理内存
|SHR|进程使用的共享内存
|S|进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数
|%CPU|进程占用CPU的使用率
|%MEM|进程使用的物理内存和总内存的百分比
|TIME+|该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。
|COMMAND|进程启动命令名称

## 运行日志输出到终端和保存起来
- - -
```
# 运行targe 目标，且同时把日志输出到终端和保存到文件log.txt
>>>$ ./targe | tee log.txt
```