---
pubDatetime: 2025-10-13 16:27:23
title: wails
featured: false
draft: false
tags:
  - wails
description: "software develop"
---

## 介绍

[wails 官网](https://wails.io/zh-Hans/)
wails 是一个基于 Go 和 web 技术的桌面应用开发框架

## 创建项目

创建项目前确保安装 Go 及 Node

```bash
 # 安装 wails
 $ go install github.com/wailsapp/wails/v2/cmd/wails@latest
 
 $ wails version
 v2.10.2

 # 检查系统依赖
 $ wails doctor
 
 # 使用 react typescript 模板
 $ wails init -n <projectName> -t react-ts

.
├── build/     # 构建输出路径
│   ├── appicon.png
│   ├── darwin/
│   └── windows/
├── frontend/   # 前端项目路径
│   ├── dist    # 前端导出路径
│   └── wailsjs # 前后端交互协议文件
├── go.mod
├── go.sum
├── main.go     # 后端项目入口
└── wails.json  # 项目配置
```

使用 nextjs 作为前端项目, frontend 文件夹改名 frontend_bak，创建新 frontend 文件夹初始化 nextjs 项目

```js
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  /* config options here */
  // next 设置 build 输出到 dist 路径
  output: "export",
  distDir: 'dist',
  reactStrictMode: true,
  compress: false,
};

export default nextConfig;
```

```json
{
  "$schema": "https://wails.io/schemas/config.v2.json",
  "name": "windows",
  "outputfilename": "windows",  // 项目名称
  "frontend:install": "pnpm install", // 前端项目安装依赖命令
  "frontend:build": "pnpm run build", // 前端项目构建命令
  "frontend:dev:watcher": "pnpm run dev", // 前端项目开发者模式命令
  "frontend:dev:serverUrl": "auto",
  "author": {
    "name": "John",
    "email": "John@example.com"
  }
}
```

```bash
 # 开发者模式
 $ wails dev

 # 构建软件 build/bin/xxx.exe
 $ wails build
```
