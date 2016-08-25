---
title: Windows route命令提示"路由添加失败:找不到元素"原因分析
date: 2012-05-17 21:01:42
tags:
  - windows route
  - 路由添加失败:找不到元素
categories:
  - windows
---


> 这篇文章是从我的百度博客迁过来的

实验需要，在电脑上弄了两个Loopback接口，并分配了不同网段的地址，但是在宿主机与客户机通信时，第一个包总是很慢，于是想到了添加静态路由，但是又发现windows下的route命令真是很难搞，总是报莫名奇妙的错误，其中一个就是添加路由时提示“路由添加失败: 找不到元素。”，折磨了很久，终于发现了个中原因：

其实原因很简单，就是命令不完整，一条完整的route命令应该像这样：

``` bat
route add 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 1 IF 31
```

其中，192.168.1.1是网关，IF 31是接口，这个接口在电脑有多个网卡时是必不可少的，因为你需要确定一条路由是从哪个接口进来和出去的，31就是执行route print后，每个网卡前面的第一个省略号前面的数字。

这样，在同时需要连接内网和外网时，可以把外网的网关设为默认网关，内网的网段一一添加到静态路由中，比方说我的路由如下：

``` bat
route -f
route add 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 1 IF 31
route add 192.168.1.0 mask 255.255.255.0 192.168.1.119 IF 17
route add 192.168.2.0 mask 255.255.255.0 192.168.2.119 IF 18
```

192.168.1.119 和192.168.2.119 分别为我的两个Loopback接口的IP地址，这个和后面的IF需要是对应的。

我的IF也贴出来如下：

``` bat
C:\Users>route print
===========================================================================
接口列表
31...20 7c 8f 61 73 d0 ......Qualcomm Atheros AR5B97 Wireless Network Adapter
20...f8 f8 f8 f8 f8 f8 ......Sangfor VPN virtual network adapter
18...02 00 4c 4f 4f 50 ......Microsoft Loopback Adapter #2
17...02 00 4c 4f 4f 50 ......Microsoft Loopback Adapter
12...20 6a 8a 39 e4 7a ......Broadcom NetLink (TM) Gigabit Ethernet
27...08 00 27 00 44 fc ......VirtualBox Host-Only Ethernet Adapter
28...00 50 56 c0 00 08 ......VMware Virtual Ethernet Adapter for VMnet8
1...........................Software Loopback Interface 1
13...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter
15...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
10...00 00 00 00 00 00 00 e0 Microsoft Teredo Tunneling Adapter
14...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
```

windows的route命令帮助写的真烂，坑爹啊，折腾好几个晚上了，就这个问题。

