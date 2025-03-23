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

使用 U 盘安装 Windows 系统, [rufus 格式化创建启动盘](https://rufus.ie/zh/)  [安装 WINDOWS 11 镜像](https://www.microsoft.com/zh-cn/software-download/windows11)  

```powershell
# 控制面板 >> 程序 >> 启用或关闭 Windows 功能 >> 启用虚拟机平台/适用于 Windows 的 Linux 子系统
# 打开 Windows 更新 >> 传递优化(提升安装 wsl 速度)
wsl --install
``

## 网络配置

设置 WSL 与 windows 镜像网络互通, WSL 与 windows IP 一致, 端口互通, 无需端口映射

```bash
​memory=8GB：限制WSL2虚拟机使用的内存最大为8GB。这可以防止WSL占用过多宿主机的内存资源，特别是在运行内存密集型应用时。
​processors=8：分配8个虚拟CPU核心给WSL2，提升多线程任务的性能。
​autoMemoryReclaim=gradual：自动回收未使用的内存，采用渐进式策略，平衡性能和内存占用。
​networkingMode=mirrored：启用镜像网络模式，使WSL直接使用宿主机的IP和端口，无需额外转发。
​dnsTunneling=true：通过Windows主机的DNS设置，确保WSL内的DNS解析与主机一致。
​firewall=true：启用Windows防火墙规则，管理WSL的网络流量。
​autoProxy=true：自动同步Windows的代理设置到WSL，方便开发环境中的网络请求。
​sparseVhd=true：启用稀疏虚拟硬盘，自动释放未使用的磁盘空间，优化存储。
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
```

## 切换安装位置

```powershell
# 显示所有 WSL 子系统
wsl -l -v
  NAME                   STATE           VERSION
* Debian                 Running         2
  docker-desktop         Running         2
  docker-desktop-data    Running         2

# 导出 WSL 子系统
wsl --shutdown
wsl --export Debian d:\wsl\debian.tar

# 注销 WSL 子系统
wsl --unregister Debian

# 导入 WSL 子系统, 设置安装位置
wsl --import Debian d:\wsl\debian d:\debian.tar

# 重新进入机器默认是 root 用户
```

## 配置 ssh

debian 默认未安装 ssh 服务, 需要手动安装  
`apt install openssh-server -y`

```bash
 $ vi /etc/ssh/sshd_config                       # 编辑 ssh 系统配置文件(安装 openssh 后才有该文件)
 
 #Port 22
 Port 2222                                       # 修改 ssh 服务端口

 #PermitRootLogin prohibit-password
 PermitRootLogin yes                             # 允许远程 root 用户登录

 #PasswordAuthentication no
 PasswordAuthentication yes                      # 允许用户密码作为口令验证

 $ systemctl restart sshd                        # 重启 ssh 服务，令配置生效
 $ service sshd restart                          # 重启 ssh 服务，令配置生效
```

```bash
 $ /usr/sbin/sshd -T                             # 测试 ssh 配置文件 /etc/ssh/sshd_config
 > sshd: no hostkeys available -- exiting.       # 提示没有 hostkey

 $ ssh-keygen -A                                 # 生成 hostkey
 $ systemctl restart sshd                        # 重启 ssh 服务，令配置生效
```

启用 `systemctl`, 修改 `/etc/wsl.conf` 文件, 重启 wsl

```bash
 $ vi /etc/wsl.conf
 [boot]
 systemd=true
```
