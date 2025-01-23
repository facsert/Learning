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


// 结构字段必须大写开头(外部调用赋值), 使用 tags 指定表对应的字段
// 结构体只需定义需要使用的列, GORM 在查询数据时会自动根据结构体字段进行取值
type Article struct {
    Id      int    `gorm:"primaryKey;autoIncrement"`
    Title   string `gorm:"column:title"`
    Content string `gorm:"column:content"`
}

// 自定义的表名, gorm 可通过 Article 实例方法定位表
func (Article) TableName() string {
    return "article"
}

// Table 自定义表名
database.DB.Table("article").Find(&articles)
```

注意:

- GORM 使用一个名为 ID 的字段作为每个模型的默认主键
- GORM 默认将结构体转为蛇形命名加上复数形式作为表名, 如 Article 默认其在数据库表名为 articles
- GORM 自动将结构体字段名称转换为蛇形命名作为数据库中的列名, 使用 `column` 标记自定义列名
- GORM 默认使用字段 CreatedAt 和 UpdatedAt 来自动跟踪记录的创建和更新时间

定义结构体用于确定表信息及使用字段映射, 钩子, 自动迁移功能等  
另外可以使用 `Table()` 自定义表名, `map[string]any` 接收数据

## 查询

```go
// 查询单个数据, 主键升序 First(第一条), Last(最后一条), Take(随机一条)
var article Article
database.DB.First(&article)
database.DB.Last(&article)
database.DB.Take(&article)

// First, Last, Take 没有数据会返回错误 gorm.ErrRecordNotFound
result := database.DB.First(&article)
if res.Error == gorm.ErrRecordNotFound {
    fmt.Println("no data")
}


// 查询所有数据 Find
var articles []Article
database.DB.Find(&articles)

// Limit 限制查询条数, 规避 ErrorRecordNotFound 错误
database.DB.Limit(1).Find(&articles)

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

无结构体查询, 使用 Table 确定表, map 接收数据

```go
// 无主键排序, First Last 无法使用, 获取所有字段
var article map[string]any
database.DB.Take(&article)

// Where 条件查询
var articles map[string]any
database.DB.Table("article").Where("title = ?", "gorm").Find(&articles)
```

## 创建

```go
// 创建 article 对象写入表(id 是自增字段无需赋值)
article := Article{Title: "go gorm", Conyent: "gorm tutorial"}
result := database.DB.Create(&article)

// article 写入表后, id 取值
article.Id

// 执行吗命令 error 或 nil
result.Error

// 插入记录数量
result.RowsAffected


// 插入多条数据
articles := []Artilce{
    {Title: "go gorm", Content: "gorm tutorial"},
    {Title: "go gorm", Content: "gorm tutorial"},
}
result := database.DB.Create(&articles)
```

无结构体创建, 使用 Table 确定表, map 设置数据

```go
// 创建单条数据
database.DB.Table("article").Create(&map[string]any{
    "title": "go gorm", "content": "gorm tutorial",
})

// 创建多条数据
database.DB.Table("article").Create(&[]map[string]any{
    {"title": "go gorm", "content": "gorm tutorial"},
    {"title": "go gorm", "content": "gorm tutorial"},
})
```

## 删除

```go
// 删除单条记录 DELETE FROM article WHERE id = 4;
database.DB.Delete(&Article{}, 4)

// 批量删除
database.DB.Where("title LIKE ?", "%gorm%").Delete(&Article{})

// 无模型删除数据
database.DB.Table("article").Where("id = ?", 5).Delete(nil)
```

## 更新

```go
// 更新单列
database.DB.Model(&Article{}).Where("id = ?", 3).Update("title", "Golang Gorm")

// 更新多列 使用结构体映射多列, 零值字段不更新
// UPDATE article SET title='Gorm' content='Golang Gorm' WHERE title='gorm';
database.DB.Model(&Article{}).Where("title = gorm").Updates(Article{Tile: "Gorm", Content: "Golang Gorm"})

// 无结构体更新
database.DB.Table("article").Where("id IN (?)", []int{1,2,3}).Updates(map[string]any{
    "title": "go gorm", "content": "gorm tutorial"
})
```

## 原生 sql

查询使用 `Raw` 可以返回查询数据, 使用 `Exec` 不返回数据

```go
// 使用原生 sql 语句
var articles []map[string]any
database.DB.Raw("SELECT * FROM article WHERE id < ? AND title = ?", 4, "gorm",).Scan(&articles)

// 使用 map 做变量替换 (表名和列名无法使用变量替换)
database.DB.Raw("SELECT * FROM article WHERE id < @id AND title = @title",
    map[string]any{"id": 4, "title": "gorm"},
).Scan(&articles)

// 删除 插入 更新不返回数据使用 Exec
database.DB.Exec("DELETE FROM article WHERE id = ?", 5)

// postgres 支持 RETURNING 可以接收返回值
var article map[string]any
database.DB.Raw("DELETE FROM article WHERE id = ? RETURNING *", 5).Scan(&article)
```
