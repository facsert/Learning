---
author: facsert
pubDatetime: 2024-04-29 22:39:27
title: React NextJs
slug: React NextJs
featured: false
draft: false
tags:
  - React
  - NextJs
description: "React NextJs"
---

## 介绍

[Next.js](https://nextjs.org/) 是一个用于创建 React 应用的框架，它提供了一些额外的功能，如路由、数据预取、静态站点生成等

## 安装

```bash
 # 创建项目, 默认挂载 http://localhost:3000
 $ pnpm create next-app@latest frontend --yes
 $ cd frontend
 $ pnpm dev
```

## 文件结构

|路径/文件|描述|
|-|-|
|`app`|项目页面文件|
|`hooks`|存放自定义 hooks|
|`lib`|存放公用组件|
|`public`|存放静态资源, 如图片|
|`next.config.js`|NextJs 配置文件|
|`package.json`|项目依赖包|

```bash
 # NextJs 文件结构如下
 app/                                  # 路由根目录
  |- page.tsx
  |- layout.tsx
  |- home/
  |   |- page.tsx
  |   |- layout.tsx
 hooks/                                 # 存放自定义 hooks
   use-mobile.tsx
 lib/                                   # 存放公用组件
  |- utils.ts
 public/                                # 静态资源
  |- favicon.ico
  |- next.svg
 next.config.js                         # 配置文件
 package.json                           # 项目依赖包
```

## 路由

NextJs 基于文件系统定义路由

- page.tsx 为页面路由入口文件
- layout.tsx 为页面布局文件
- (dir) 使用括号包含忽略层级
- [dir] 使用中括号包含可变层级

```bash
# http://localhost:3000/
app/page.tsx 

# http://localhost:3000/home
app/home/page.tsx

# http://localhost:3000/about
app/about/page.tsx

# () 可将多个文件分类归档而不影响路由
# http://localhost:3000/table
app/(group)/table/page.tsx


# [] 匹配可变层级, 按路由参数渲染页面
# http://localhost:3000/table/1
app/table/[id]/page.tsx

# http://localhost:3000/users/john/about
app/users/[name]/about/page.tsx
```

## 配置

`package.json` 项目依赖包管理  

```json
{
  "name": "frontend",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint"
  }
}
```

```bash
 # 对应 next dev, 开发模式启动项目, 支持热更新
 $ pnpm dev

 # 对应 next build, 构建项目
 $ pnpm build

 # 对应 next start, 启动项目
 $ pnpm start
```

`next.config.js` 项目配置文件

```js
const nextConfig: NextConfig = {
  /* config options here */
  distDir: 'build',  // 构建输出路径
  reactStrictMode: true, // 开启严格模式
  
  output: 'standalone', // 打包必要的文件生成最小部署包
  output: 'export', // 静态站点生成
};
```

构建 docker 镜像, 使用默认配置, 编译结果存放在 `.next/` 目录下

```js
const nextConfig: NextConfig = {
  /* config options here */
  output: 'standalone',
};
```

```bash
 # 复制静态文件到构建路径下
 $ cp -r public .next/standalone/ 
 $ cp -r .next/static .next/standalone/.next/

 # 拉起服务
 $ node .next/standalone/server.js
```

编译项目

## 中间件

项目根目录(`package.json` 同级)创建 `proxy.ts`  
中间件常用于请求拦截, 身份认证, 页面跳转

```js
import { NextRequest, NextResponse } from "next/server"

export default function proxy(req: NextRequest) {
  // 检查 cookie 中是否存在 Authorization
  // 如果不存在, 重定向到登录页面
  const token = req.cookies.get("Authorization")
  const loginUrl = new URL("/login", req.url)
  if (!token) {
    return NextResponse.redirect(loginUrl)
  }
  if (req.nextUrl.pathname === "/") {
    return NextResponse.redirect(new URL("/pages", req.url))
  }
  return NextResponse.next()
};

// 匹配规则
export const config = {
  matcher: [
    '/pages/:path*', // 匹配 /pages 路径下的所有页面
    '/((?!login|sign|_next/static|_next/image|.*\\.png$).*)', // 匹配除了 /login, /sign, /api, /_next/static, /_next/image, *.png
  ]
}

```
