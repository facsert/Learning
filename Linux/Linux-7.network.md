---
author: facsert
pubDatetime: 2024-09-23  14:20:43
title: Linux network
slug: Linux network
featured: false
draft: false
tags:
  - Linux
  - network
description: "网络"
---

## 介绍

## IP 地址

- IPV4: 32 位二进制(2^32), 通常转化为 4 组(一组0-255)10进制表示(256^4=2^32), 从 0.0.0.0 到 255.255.255.255, 地址已用尽
- IPV6: 128 位二进制(2^128), 通常用 8 组 4 位 16 进制(16^4^8=2^128), 0000:0000:00..(8组 32个0) 到 FFFF:FFFF:FF..(8组 32个F)

```bash
 # linux macos 查看 IP 地址
 $ ip a
 $ ifconfig

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.31.239.107  netmask 255.255.240.0  broadcast 172.31.239.255
        inet6 fe80::215:5dff:fe0d:d89f  prefixlen 64  scopeid 0x20<link>
        ether 00:15:5d:0d:d8:9f  txqueuelen 1000  (Ethernet)
        RX packets 77958  bytes 50386965 (50.3 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 39222  bytes 5389894 (5.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 754324  bytes 666443571 (666.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 754324  bytes 666443571 (666.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
 
 # windows 查看 IP 地址
 $ ipconfig

以太网适配器 以太网 2:

   连接特定的 DNS 后缀 . . . . . . . .: openstacklocal 
   IPv4 地址 . . . . . . . . . . . . : 10.1.1.15
   IPv6 地址 . . . . . . . . . . . . : fe80::42e:23ba:5f50:4232%16
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 10.1.112.1

以太网适配器 vEthernet (WSL (Hyper-V firewall)):

   连接特定的 DNS 后缀 . . . . . . . :
   IPv4 地址 . . . . . . . . . . . . : 172.31.224.1
   子网掩码  . . . . . . . . . . . . : 255.255.240.0
   默认网关. . . . . . . . . . . . . :
```

## IPV6 格式

IPV6 有32字符, 为简便读写设定了一些压缩格式简写, 机器可以按规则补全恢复原有的 IPV6 地址

```bash
 # 一组 4 个字符或连续多组全为 0 简写为 ::(避免多种恢复结果, 一个IPV6地址只能出现 1 次)
 0000:0000:0000:0000:0000:0000:0000:0000 -> ::
 
 # 此种简写会导致补位出现多种情况, 所以不允许多个 ::
 0000:0000:0001:0000:0000:0001:0000:0000 -> ::0001::0001::

 # 16 进制中 00A0 = A0, 数字中前置的 0 不影响大小, 也适用于 IPV6
 0000:0000:0000:0001:0000:0000:0002:0000 -> ::1:0:0:2:0
```
