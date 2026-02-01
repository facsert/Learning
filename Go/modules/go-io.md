---
pubDatetime: 2023-07-28 09:31:54
title: Go IO
slug: Go IO
featured: false
draft: false
tags:
  - Go
  - io
description: "Go 数据 io"
---

## 介绍

golang 中数据操作经常遇到 io.Reader 和 io.Writer 接口类型参数

- io.Reader
- io.Writer

```go
// 定义 Reader 类型接口方法
// 传入字节切片 p, 读取 len(p) 字节长度的内容写入 p, 返回写入的字节数 n(n == len(p)), 错误 err
type Reader interface {
    Read(p []byte) (n int, err error)
}

// 定义 Writer 类型接口方法
// 输入字节切片 p, 写入 p, 返回写入的字节数 n(n == len(p)), 错误 err
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

## IO 包

- io: io.Reader 和 io.Writer 接口类型定义, 类似于类
- os: io.Reader 和 io.Writer 接口的系统实现, 通过 syscall 操作系统资源
- bufio: 在内存中缓存数据, 满足条件时调用系统接口操作系统资源, 减少系统调用, 提升性能
- bytes: 在内存中创建一个可扩大的空间存储数据, 对象本身既保存有数据, 也有操作数据的方法

```go
// os.File 类型实现了 io.Reader 和 io.Writer 接口方法
// os.File 保存文件句柄和文件名, 通过系统接口读取文件内容
// os.File.Read 每次读取都会触发系统调用
type File struct {
    *file // os specific
}

type file struct {
    pfd        poll.FD
    name       string
    dirinfo    atomic.Pointer[dirInfo] // nil unless directory being read
    appendMode bool                    // whether file is opened for appending
}

func (f *File) Read(b []byte) (n int, err error)

func (f *File) Write(b []byte) (n int, err error)


// 创建文件对象
fs, _ := os.Open("file.txt")

// 直接读取文件数据
var b [1024]byte
n, _ := fs.Read(b)
```

```go
// bufio.Reader 创建 Reader 对象, 通过缓存读取数据
// buf 是内存缓冲区, 默认 4KB, buf 内容读取完后才触发系统调用填满 buf
type Reader struct {
    buf          []byte
    rd           io.Reader // reader provided by the client
    r, w         int       // buf read and write positions
    err          error
    lastByte     int // last byte read for UnreadByte; -1 means invalid
    lastRuneSize int // size of last rune read for UnreadRune; -1 means invalid
}

const defaultBufSize = 4096

func NewReader(rd io.Reader) *Reader {
    return NewReaderSize(rd, defaultBufSize)
}

func (r *Reader) Read(p []byte) (n int, err error)

func (r *Reader) Write(p []byte) (n int, err error)


// 创建 Reader 对象, 通过缓存读取数据
buf := bufio.NewReader(fs)
var b [1024]byte
n, _ := buf.Read(b)
```

```go
// bytes.Buffer 对象本身存储数据, 且包含操作数据的方法
// 读取写入都只在内存中, 不会触发系统调用
type Buffer struct {
    buf      []byte // buf是一个字节切片，
                    //它的内容是buf[off:len(buf)]，即从off到buf末尾的部分。
    off      int    // off是一个int类型，
                    // 表示下一次读取操作应该在&buf[off]处开始，
                    // 下一次写入操作应该在&buf[len(buf)]处开始。
    lastRead readOp // lastRead是一个readOp类型的变量，
                    // 表示最后一次读取操作的类型，以便Unread*方法可以正确地工作。
}

func (b *Buffer) Read(p []byte) (n int, err error)

func (b *Buffer) Write(p []byte) (n int, err error)

// 创建 Buffer 对象, 可以直接读写数据, 返回最终结果
var buf bytes.Buffer
buf.Write([]byte("hello world"))
buf.String()
```
