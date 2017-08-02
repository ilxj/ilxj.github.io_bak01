---
title: PC小技巧
date: 2017-07-01 19:20:11
tags: [PC工具]
categories: 工具
top: 1
---
## Win7 开热点
1. 输入命令：```netsh wlan set hostednetwork mode=allow ssid=livdran2012 key=100100100``` 。
```mode:```是否启用虚拟WiFi网卡，改为disallow则为禁止。
```ssid:```无线网名称，推荐最好用英文。（此处以livdran2012为例，可自由设置）
```key:```无线网密码，限定八个以上字符。（此处以100100100为例，可自由设置）

<!--more-->

2. 运行成功后，打开网络和共享中心，点击左侧更改适配器设置。
3. 进入适配器设置就会发现多出了一个网卡为“Microsoft Virtual WiFi Miniport Adapter”的无线网络连接2。若没有，则尝试更新无线网卡驱动。
为方便起见，可将无线网络连接2重命名为**"VirtualWiFi"**。
3. 在网络连接里， 右键单击已连接到Internet的网络连接，选择“属性”→“共享”，勾上“允许其他网络用户通过此计算机的Internet连接来连接(N)”并选择“虚拟WiFi”。
确定之后，提供共享的网卡图标旁会出现“共享的”字样，表示“宽带连接”已共享至**"VirtualWiFi"**
![网络共享][1]
![共享标志][2]
4.继续命令行输入:```netsh wlan start hostednetwork```，按回车键运行。
最后，虚拟WiFi的红叉消失，WiFi基站已组建好并启动，用支持WiFi的设备搜索到自己建立的无线网络（如例中livdran2012）,输入自己设置的密码（如例中100100100），即可共享上网了。


  [1]: http://static.zybuluo.com/AlexLin/7tftyoeqj79obckwcylz4ret/image.png
  [2]: http://static.zybuluo.com/AlexLin/e0ca3mszpdbqmzp7vhvut012/image.png