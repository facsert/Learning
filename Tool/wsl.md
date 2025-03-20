---
pubDatetime: 2022-12-31 19:20:23
title: wsl
slug: wsl
featured: false
draft: false
tags:
  - wsl
  - Tool
description: wsl 是一个使用广泛的文件编辑器
---

## 介绍

## 安装

打开传递优化

```powershell
wsl --install
``

## 网络配置

设置 WSL 与 windows 公用网络

```bash
​memory=8GB：限制WSL2虚拟机使用的内存最大为8GB。这可以防止WSL占用过多宿主机的内存资源，特别是在运行内存密集型应用时。
​processors=8：分配8个虚拟CPU核心给WSL2，提升多线程任务的性能。
​autoMemoryReclaim=gradual：自动回收未使用的内存，采用渐进式策略，平衡性能和内存占用。
​networkingMode=mirrored：启用镜像网络模式，使WSL直接使用宿主机的IP和端口，无需额外转发。
​dnsTunneling=true：通过Windows主机的DNS设置，确保WSL内的DNS解析与主机一致。
​firewall=true：启用Windows防火墙规则，管理WSL的网络流量。
​autoProxy=true：自动同步Windows的代理设置到WSL，方便开发环境中的网络请求。
​sparseVhd=true：启用稀疏虚拟硬盘，自动释放未使用的磁盘空间，优化存储。
​ignoredPorts=22,2222：保留Windows主机的22和2222端口，防止WSL服务占用这些端口。
```

```toml
# Settings apply across all Linux distros running on WSL 2
[wsl2]

# Limits VM memory to use no more than 4 GB, this can be set as whole numbers using GB or MB
memory=8GB

# Sets the VM to use two virtual processors
processors=8

[experimental]
autoMemoryReclaim=gradual # 开启自动回收内存，可在 gradual, dropcache, disabled 之间选择
networkingMode=mirrored # 开启镜像网络
dnsTunneling=true # 开启 DNS Tunneling
firewall=true # 开启 Windows 防火墙
autoProxy=true # 开启自动同步代理
sparseVhd=true # 开启自动释放 WSL2 虚拟硬盘空间
ignoredPorts=22,2222
```

##
