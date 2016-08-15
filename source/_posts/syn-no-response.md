---
title: 服务器不响应SYN的原因
date: 2016-08-15 19:01:42
tags: 
  - SYN
  - 不响应SYN
  - iptables
categories: 
  - linux
---

最近碰到过两次服务器不响应SYN包的情况，刚开始感觉真的是莫名其妙，解决过程也是挺纠结的，记录一下：

- iptables问题

同样的iptables策略在阿里云服务器上没有问题，但是在AWS EC2上就有问题，现象之一就是外部服务器可以跟服务器端口通信，但是同一VPC内部的服务器访问时，端口不通；

经过排查，问题定位在iptables策略上，只能一条一条排查，最终发现问题出在这两条策略上：

``` bash
iptables -A INPUT -p tcp -m ttl --ttl-lt 5 -j DROP
iptables -A INPUT -p tcp -m ttl --ttl-gt 128 -j DROP
```

原来是Amazon Linux系统的TTL默认值是255，不在5 - 128 范围之内，所以被服务器直接丢弃了，这两条策略也是从网上不知道哪里抄来的，把服务器收到的包TTL限制在5 - 128之间，想想真是后患无穷啊！


- net.ipv4.tcp_tw_recycle

这个内核参数，网上搜搜，都建议打开，可以快速回收timewait连接，刚开始我也是设置的打开，导致的问题就是，公司内部访问服务器时，有的设备可以通信，有的设备端口不通，我的建议是不打开，否则nat网络后端的设备访问时就会出现异常，详细原因就不说了，自行google。

总结一下，网上的参数不可以随便拿来用呀，一定要搞清楚再用。


