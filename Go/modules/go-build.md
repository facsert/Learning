---
pubDatetime: 2023-04-02 15:28:43
title: Go Build
slug: Go Build
featured: false
draft: false
tags:
  - Go
description: "Go 构建项目"
---

## 介绍

Go 可以直接将项目编译成可执行文件, 且支持交叉编译, 编译成 Linux/Mac/Win 平台, Arm/x86 CPU 架构可执行文件

## 交叉编译

Go 使用 `go build` 将项目按当前系统及架构编译对应二进制文件  
使用指定参数, 可编译成其它平台或架构二进制文件, 如在 linux 编译出 exe 文件

```bash
 # 自动找到 main 包的 main 函数入口, 以项目名称为文件名称
 $ go build

 # 在 linux 下编译, 指定入口文件, 编译结果文件名称
 $ go build -o buildLinux
 $ go build main.go -o buildLinux

 # 指定编译文件适配的系统和 CPU 架构
 $ GOOS=windows GOARCH=amd64 go build
 $ GOOS=linux GOARCH=arm64 go build

 # 查看支持的平台和架构
 $ go tool dist list

 # 使用 Go 查询本机平台及CPU型号
 $ go env |egrep "GOOS|GOARCH"
 
 # linux/Mac 查询本机信息
 $ uname -a
```

常见的平台和 CPU 架构

|平台|Linux|Mac|Windows|
|:-:|:-:|:-:|:-:|
|GOOS|linux|darwin|windows|

|CPU架构|Amd32位|Amd 64位|Arm|Arm64位|
|:-:|:-:|:-:|:-:|:-:|
|GOARCH|386|amd64|arm|arm64|

注: 由于 Darwin SDK 闭源, Linux/Win 无法编译出 Mac 应用, 需在 Mac 上构建

## Windows 构建

构建 Windows 工具时, 工具执行需要申请权限, 工具需配置图标, 配置信息  
可以使用 [go-winres](https://github.com/tc-hib/go-winres) 一键配置

```bash
 # 下载工具, 该工具是一个命令行工具
 $ go install github.com/tc-hib/go-winres@latest
 
 # 在项目根目录执行, 生成 winres 路径, 路径下有默认图标, 和配置
 $ go-winres init
 
 # 修改好图标和配置后, 执行命令生成对应的 syso 文件
 $ go-winres make

 # 自动将 syso 文件嵌入工具
 $ go build
```

重要配置

```json
{
  "RT_MANIFEST": {
    "#1": {
      "0409": {
        "identity": {
          "name": "",    // 应用名称
          "version": ""  // 应用版本
        },
        "description": "",
        "minimum-os": "win7", // windows 最低版本
        "execution-level": "as invoker", // 执行权限
      }
    }
  }
}
```

execution-level 表示执行需要的权限

- "as invoker" 默认权限
- "highest" 当前用户所有权限
- "Administrator" 管理员权限, 自动触发 UAC 申请
