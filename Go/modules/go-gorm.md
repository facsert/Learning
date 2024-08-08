---
author: facsert
pubDatetime: 2023-07-10 09:29:19
title: Go Gorm
slug: Go Gorm
featured: false
draft: false
tags:
  - Go
  - Gin
description: "Go Gorm 数据库 orm"
---

## 安装

[GORM 官网](https://gorm.io/)

```bash
$ go get -u gorm.io/gorm
> go: added gorm.io/gorm v1.25.11

$ go get -u gorm.io/driver/postgres              # postgres 驱动
$ go get -u gorm.io/driver/mysql                 # mysql 驱动
$ go get -u gorm.io/driver/sqlite                # sqlit 驱动
```

## 连接

连接 mysql 数据库

```go
import (
    "fmt"
    "log"

    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)

const (
    host     = "localhost"
    port     = 3306
    username = "root"
    password = "admin"
    dbname   = "db"
)

var DB *gorm.DB

func main() {
    dsn := fmt.Sprintf(
        "%s:%s@tcp(%s:%d)/%s?charset=utf8mb4&parseTime=True&loc=Local",
        username, password, host, port, dbname,
    )
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
    if err != nil {
        log.Fatalf("failed to connect database: %v", err)
    }
    DB = db
}
```

连接 postgres 数据库

```go
import (
    "fmt"
    "log"

    "gorm.io/driver/postgres"
    "gorm.io/gorm"
)

var DB *gorm.DB

const (
    host         = "localhost"
    port         = 5432
    username     = "postgres"
    password     = "password"
    databaseName = "postgres"
)

func Init() {
    dsn := fmt.Sprintf(
        "host=%s user=%s password=%s dbname=%s port=%d sslmode=disable TimeZone=Asia/Shanghai",
        host, username, password, databaseName, port,
    )

    db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{})
    if err != nil {
        log.Fatalf("failed to connect database: %v", err)
    }
    DB = db
}
```

## 模型

gorm 使用结构体和数据库表做映射

```go
// gorm 内置了 gorm.Model, 可以嵌入自己模型
type Model struct {
  ID        uint `gorm:"primary_key"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt *time.Time
}


// 结构字段必须大写开头(外部调用)
// 指定表对应的字段
type Article struct {
    Id      int    `gorm:column:id`
    Title   string `gorm:column:title`
    Content string `gorm:column:content`
}
```

注意:

- GORM 使用一个名为ID 的字段作为每个模型的默认主键
- GORM 默认将结构体转为蛇形命名加上复数形式作为表名, 如 User 默认其在数据库表名为 users, 使用 `DB.Table()` 自定义表名
- GORM 自动将结构体字段名称转换为蛇形命名作为数据库中的列名, 使用 `column` 标记自定义列名
- GORM 使用字段 CreatedAt 和 UpdatedAt 来自动跟踪记录的创建和更新时间

## CURD

### 查询

```go
var articles []Article
res := database.DB.Table("article").Select("id", "title", "content").Find(&articles)
if res.Error != nil {
    fmt.Printf("err: %#v\n", res.Error)
}
for _, article := range articles {
    fmt.Printf("node: %#v\n", article)
}
```
