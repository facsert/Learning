---
pubDatetime: 2023-04-07 15:02:16
title: Linux System
slug: Linux System
featured: false
draft: false
tags:
  - Linux
description: "Linux 系统"
---

## Table of Contents

## 系统

### [uname](https://linux.alianga.com/c/uname.html)

Unix name

```bash
 $ uname -a                                      # 打印操作系统的所有信息
 > Linux master 3.10.0-1160.81.1.el7.x86_64 #1 SMP Fri Dec 16 17:29:43 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```

### [date](https://linux.alianga.com/c/date.html)

```bash
 $ date                                          # 现实系统时间
 > Fri 07 Apr 2023 02:16:35 AM EDT

 $ date +"%Y-%m-%d %H:%M:%S"                     # 格式化输出时间
 > 2023-04-07 02:16:52

 $ date -s "01:01:01 2022-08-09"                 # 设置时间
```

### [df](https://linux.alianga.com/c/df.html)

disk free

```bash
 $ df                                            # 显示系统磁盘占用信息, 默认以 KB 为单位
 $ df -h                                         # 容量数据以 MB 为单位

 $ df -h                                         # 容量信息智能添加单位
 > Filesystem      Size  Used Avail Use% Mounted on
 > udev            3.9G     0  3.9G   0% /dev
 > tmpfs           796M   41M  755M   6% /run
 > /dev/sda1        58G   17G   39G  30% /
```

### [du](https://linux.alianga.com/c/du.html)

disk usage: 查看文件或文件夹占用大小

```bash
 $ du <arg> <file>                               # 显示目录占用空间大小
 $ du -s <file>                                  # --summarize 只显示总计占用
 $ du -h <file>                                  # --human-readable 添加单位

 $ du *                                          # 列出所有文件及大小
 > 97764   go20.tar.gz
 > 4       host.txt
 > 24      mongo.tar.gz

 $ du -h                                         # 智能显示单位
 > 4.0K    ./test
 > 834M    .
```

### [free](https://linux.alianga.com/c/free.html)

```bash
 $ free                                          # 显示系统内存占用(KB)
 $ free -m                                       # 以 MB 为单位显示
 $ free -g                                       # 以 GB 为单位显示

 $ free -h                                       # 智能显示单位
                total        used        free      shared  buff/cache   available
 > Mem:          7.8Gi       1.2Gi       5.3Gi        41Mi       1.3Gi       6.3Gi
 > Swap:         974Mi          0B       974Mi
```

### [ps](https://linux.alianga.com/c/ps.html)

process status: 进程状态

```bash
 $ ps <opt>                                      # 显示系统进程状态
 $ ps aux                                        # 显示系统所有进程的详细信息
 > USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
 > root         1  0.0  0.1 104188 10472 ?        Ss   Apr02   0:03 /sbin/init
 > root         2  0.0  0.0      0     0 ?        S    Apr02   0:00 [kthreadd]
 > root         3  0.0  0.0      0     0 ?        I<   Apr02   0:00 [rcu_gp]
```

### [systemctl](https://linux.alianga.com/c/systemctl.html)

systemctl control

```bash
 $ systemctl <opt> <service>                     # 设置某项服务
 $ systemctl start <service>                     # 启动nfs服务
 $ systemctl enable <service>                    # 设置开机自启动
 $ systemctl disable <service>                   # 停止开机自启动
 $ systemctl status <service>                    # 查看服务当前状态
 $ systemctl restart <service>                   # 重新启动某服务
 $ systemctl list-units --type=service           # 查看所有已启动的服务

 $ systemctl status docker
 > ● docker.service - Docker Application Container Engine
 >   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
 >   Active: active (running) since Sun 2023-04-02 20:24:38 EDT; 4 days ago
 >     Docs: https://docs.docker.com
 > Main PID: 704 (dockerd)
 > ......
```

### [netstat](https://linux.alianga.com/c/netstat.html) : network statistics

```bash
 $ netstat <opt>                                 # 显示网络端口信息
 $ netstat -a                                    # 显示所有网络端口信息
 $ netstat -t                                    # 显示 TCP 网络端口信息
 $ netstat -u                                    # 显示 UDP 网络端口信息
 $ netstat -l                                    # --listening 显示处于监听状态的端口

 $ netstat -anlp | grep 22
 > tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      577/sshd
 > tcp        0      0 10.0.2.15:22            10.0.2.2:61110          ESTABLISHED 19205/sshd: root@no
 > tcp6       0      0 :::22                   :::*                    LISTEN      577/sshd
```

### [ifconfig](https://linux.alianga.com/c/ifconfig.html)

network interfaces configuring

```bash
 $ ifconfig                                      # 显示网络接口配置和参数
 > enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
 >       inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
 >       inet6 fe80::a00:27ff:fe2e:3801  prefixlen 64  scopeid 0x20<link>
 >       ether 08:00:27:2e:38:01  txqueuelen 1000  (Ethernet)
 >       RX packets 1226712  bytes 239728472 (228.6 MiB)
 >       RX errors 0  dropped 0  overruns 0  frame 0
 >       TX packets 857854  bytes 146145492 (139.3 MiB)
 >       TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
 > ......
```

### [kill](https://linux.alianga.com/c/kill.html)

```bash
 $ kill <opt> <pid>                              # 杀死进程
 $ kill -9 <pid>                                 # 强制杀死 <pid> 进程

 $ killall <name>                                # 杀死所有名字为 <name> 进程
 $ pkill -o <name>                               # 杀死 <name> 进程中进程号最小的
 $ pkill -n <name>                               # 杀死 <name> 进程中进程号最大的

 $ sleep 300 &                                   # 后台执行 sleep 指令
 > [1] 23287                                     # 系统返回后台的进程 pid
 $ kill -9 23287                                 # 根据 pip 杀死指定进程
 > [1]  + 23287 killed     sleep 300
```

## 硬盘挂载

设置硬盘临时挂载

```bash
 # 查看所有块设备 (sdb 未挂载)
 $ lsblk
  NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
  loop0    7:0    0   62M  1 loop /snap/core20/1587
  loop1    7:1    0 79.9M  1 loop /snap/lxd/22923
  loop2    7:2    0   47M  1 loop /snap/snapd/16292
  sda    252:0    0   40G  0 disk
  └─sda1 252:1    0   40G  0 part /
  sdb    252:16   0  200G  0 disk

 $ df -h
  Filesystem      Size  Used Avail Use% Mounted on
  tmpfs           1.6G  1.1M  1.6G   1% /run
  /dev/sda1        40G  6.4G   31G  18% /
  tmpfs           7.7G     0  7.7G   0% /dev/shm
  tmpfs           5.0M     0  5.0M   0% /run/lock
  tmpfs           1.6G  4.0K  1.6G   1% /run/user/0

 # 挂载磁盘到 /root/disk 路径(路径必须存在, 重启则挂载失效)
 $ mount /dev/sdb /root/disk
```

设置硬盘永久挂载

```bash
 # 查看机器硬盘 (sdb 未挂载)
 $ lsblk

 $ df -h

 # 获取设备 UUID(返回为空, 则先格式化再获取 UUID)
 $ blkid /dev/sdb1
 /dev/sdb: UUID="3b6c12s6-67e5-5bcs-a15a-ecb6c7dc68ab" BLOCK_SIZE="4096" TYPE="ext4"

 # 格式化磁盘(能获取 UUID 则跳过格式化)
 $ mkfs.ext4 /dev/sdb
  
 # 将挂载信息写入 /etc/fstab, 每次重启都自动挂载
 # <file system> <mount point> <type>  <options> <dump>    <pass>
 # UUID作为设备id  挂载路径     文件类型  挂载选项  dump 备份  根文件系统 1
 $ vi /etc/fstab
 UUID=3b6c12s6-67e5-5bcs-a15a-ecb6c7dc68ab /root/disk ext4 defaults 0 1

 # 根据 /etc/fstab 配置自动挂载
 $ mount -a

 # 卸载已挂载的盘(无进程访问挂载路径)
 $ umount /root/disk
```
