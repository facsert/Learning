---
pubDatetime: 2023-07-12 09:29:19
title: 00.Go Environment
slug: 00.Go Environment
featured: false
draft: false
tags:
  - Go
description: "Go 环境配置"
---

## Table of Contents

## 安装

[Golang 官网](https://go.dev/) 下载 Golang 最新的包  
解压安装包到 /usr/local 目录下

```bash
 $ wget https://golang.google.cn/dl/go1.20.3.linux-amd64.tar.gz
 $ tar -zxvf go1.20.3.linux-amd64.tar.gz -C /usr/local/

 $ pwd                                           # 压缩包解压到 /usr/local/go 下
 > /usr/local/go
```

将 `/usr/local/go/bin` 添加进环境目录, 使得系统在环境目录中找到 golang 的可执行文件

```bash
 $ vi ~/.bashrc
 > export GOROOT=/usr/local/go                   # 将两条命令写入 ~/.bashrc
 > export PATH=$PATH:$GOROOT/bin                 # 意为每次启动命令行都将 /usr/local/go/bin 加入环境目录
 $ SOURCE ~/.bashrc                              # 令修改后的 bashrc 生效

 $ go env -w GOPROXY=https://goproxy.cn,direct   # 更换 go 默认下载源
```

## 版本管理

使用 g 管理 go 版本

```bash
 $ curl -sSL https://raw.githubusercontent.com/voidint/g/master/install.sh | bash
 # 重启命令行, 查看可用的 g 版本
 $ source ~/.bashrc
 $ g -v
 
 # 查看远程可用的 go 版本, 安装指定版本
 $ g ls-remote
 $ g install 1.24.1
 $ g use 1.24.1
 $ go version
 > go version go1.24.1 linux/amd64
```

## 开始

使用 `go mod` 创建项目

```bash
 $ go mod init <module name>                     # go mod 初始化项目
 $ touch main.go                                 # 创建项目入口

 $ ll
 > total 12K
 > -rw-r--r-- 1 root root   22 Mar 23 03:24 go.mod
 > -rw-r--r-- 1 root root 4.2K Apr 25 03:26 main.go

 $ cat go.mod
 > module learn
 >
 > go 1.20
```

使用 go mod 创建项目会自动生成 go.mod 文件, 该文件会记录项目需要用的第三方包

```go
package main                                     // 声明包

import (                                         // 引入标准库 "fmt"
    "fmt"
)

func main() {                                    // 项目入口, 只允许一个项目入口
    fmt.Println("hello world!")                  // 打印
}
```

执行 main.go

```bash
 $ go run main.go                                # 执行 go 文件
 > hello world!
```
