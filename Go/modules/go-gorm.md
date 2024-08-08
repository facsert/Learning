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
// 查询单个数据, 主键升序 First(第一条), Last(最后一条), Take(随机一条)
var article Article
database.DB.Table("article").Select("title", "created_at", "updated_at").First(&article)
database.DB.Table("article").Select("title", "created_at", "updated_at").Last(&article)
database.DB.Table("article").Select("title", "created_at", "updated_at").Take(&article)

// First, Last, Take 没有数据返回错误 gorm.ErrRecordNotFound
result := database.DB.Table("article").Select("id", "title", "content").First(&article)
if res.Error == gorm.ErrRecordNotFound {
    fmt.Println("no data")
}


// 查询所有数据 Find
var articles []Article
database.DB.Table("article").Select("id", "title", "content").Find(&articles)

// Limit 限制查询条数, 规避 ErrorRecordNotFound 错误
database.DB.Table("article").Select("id", "title", "content").Limit(1).Find(&articles)

// 查询条数
result.RowsAffected

// 执行报错或 nil
result.Error
```

```go
// Where 查询条件  SELECT * FROM article WHERE id = 6 LIMIT 1;
database.DB.Where("id = ?", 6).First(&articles)

// Where 查询条件  SELECT * FROM article WHERE id IN (1, 2, 3);
database.DB.Where("id IN ?", []int{1, 2, 3}).Find(&articles)

// Where 查询条件  SELECT * FROM article WHERE id > 3 AND id < 8;
database.DB.Where("id > ? AND id < ?", 3, 8).Find(&articles)

// OR 查询条件 SELECT * FROM article WHERE id > 3 OR title = 'gorm';
database.DB.Where("id = 5").Or("title = 'gorm'").Find(&articles)

// 设置查询字段 Select 多个参数或数组 SELECT id, title, content FROM article;
database.DB.Select([]string{"id", "title", "content"}).Find(&articles)

// Order 排序 SELECT * FROM article ORDER BY name DESC;
database.DB.Order("name DESC").Find(&articles)

// Limit Offset 限制查询条数和偏移 SELECT * FROM article OFFSET 3 LIMIT 10;
database.DB.Limit(10).Offset(3).Find(&articles)

```
