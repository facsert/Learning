---
pubDatetime: 2025-08-07 09:29:19
title: 00.Rust introduce
featured: false
draft: false
tags:
  - Rust
description: "Rust 基础"
---

## 介绍

[Rust 官网](https://www.rust-lang.org/)

## 安装

```bash
 # 通过 rustup 安装 rust
 $ apt install rustup    # linux
 $ brew install rustup   # mac
 $ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh 

 # 查看安装版本
 $ rustc -V
 rustc 1.82.0 (f6e511eec 2024-10-15)
 $ cargo -V
 cargo 1.82.0 (8f40fc59f 2024-08-21)
```

## 创建项目

rust 提供了强大的包管理器 cargo, 可以使用 cargo 创建和执行项目

```bash
 # 创建项目, 生成两个文件
 $ cargo new hello
 hello
 ├── Cargo.toml
 └── src
    └── main.rs
 
 # 执行项目
 $ cd hello && cargo run
     Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.05s
      Running `target/debug/hello`
 Hello, world!

 # 编译后生成 target 路径, 可执行文件 target/debug/hello
├── Cargo.lock
├── Cargo.toml
├── src
│   └── main.rs
└── target
    ├── CACHEDIR.TAG
    └── debug
        ├── build
        ├── deps
        │   ├── hello-bd034b05d2221d72
        │   └── hello-bd034b05d2221d72.d
        ├── examples
        ├── hello
        ├── hello.d
        └── incremental
```

`cargo` 常用命令

```bash
 # 检查项目代码
 $ cargo check

 # 编译项目代码
 $ cargo build

 # 编译并执行项目代码
 $ cargo run
```
