---
pubDatetime: 2025-12-13 17:08:54
title: Go sqlx
slug: Go SSH
tags:
  - Go
  - database
description: "Go dayabase sqlx"
---

## 介绍

`sqlx` 是一个基于 `database/sql` 的数据库操作库，提供了一些额外的功能，如自动映射结构体到数据库字段，自动执行 SQL 语句，自动处理数据库连接池

## 基本使用

```bash
 # 安装 sqlx 及 PostgreSQL 驱动
 $ go get github.com/jmoiron/sqlx
 $ go get github.com/lib/pq
```

```go
// 定义结构体绑定数据库字段
// TABLE users
const table = `
CREATE TABLE IF NOT EXISTS users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  username VARCHAR(255) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),
  deleted_at TIMESTAMP DEFAULT NULL
);
`
type User struct {
    Id        int        `db:"id" json:"id" insert:"-" update:"-"`
    Email     string     `db:"email" json:"email" insert:"email" update:"email"`
    Username  string     `db:"username" json:"username" insert:"username" update:"username"`
    Password  string     `db:"password" json:"password" insert:"password" update:"password"`
    CreatedAt time.Time  `db:"created_at" json:"created_at" insert:"-" update:"-"`
    UpdatedAt time.Time  `db:"updated_at" json:"updated_at" insert:"-" update:"updated_at"`
    DeletedAt *time.Time `db:"deleted_at" json:"deleted_at" insert:"-" update:"deleted_at"`
}
```

```go
import (
    sql "github.com/jmoiron/sqlx"
    _ "github.com/lib/pq"
)

type DBConn struct{
    Host     string
    Port     int
    Username string
    Password string
    DBName   string
}

func Connect(conn *DBConn) (*sql.DB, error) {
    // 连接方式 1
    db, err := sql.Connect(
        "postgres", 
        fmt.Sprintf("postgres://%s:%s@%s:%d/%s?sslmode=disable",
            conn.Username,
            conn.Password,
            conn.Host,
            conn.Port,
            conn.DBName,
        )
    )
    
    // 连接方式 2
    db, err := sql.Connect(
        "postgres", 
        fmt.Sprintf("user=%s password=%s host=%s port=%d dbname=%s sslmode=disable",
            conn.Username,
            conn.Password,
            conn.Host,
            conn.Port,
            conn.DBName,
        )
    )
    
    // 设置连接池参数
    db.SetMaxOpenConns(10)
    db.SetMaxIdleConns(5)
    db.SetConnMaxLifetime(5 * time.Minute)
    return db, err
}


// 查询, 多行查询使用 Select
var users []User
db.Select(&users, "SELECT * FROM users WHERE deleted_at IS NULL")

// 查询, 单行查询使用 Get
var user User
db.Get(&user, "SELECT * FROM users WHERE id = $1 AND deleted_at IS NULL", id)

// 插入单行或多行, 使用 NameExec 通过结构体 tag 的 db 字段自动匹配(支持使用 map)
user := User{"email", "username", "password"}
db.NameExec("INSERT INTO users (email, username, password) VALUES (:email, :username, :password)", user)

// 更新, 使用 NameExec 通过结构体 tag 的 db 字段自动匹配(支持使用 map)
user := User{1, "email", "username", "password"}
db.NameExec("UPDATE users SET email = :email, username = :username, password = :password WHERE id = :id", user)

// 删除, 更新 deleted_at 字段逻辑删除, $1 为占位符
id := 1
db.Exec("UPDATE users SET deleted_at = NOW() WHERE id = $1 AND deleted_at IS NULL", id)
```

## 示例

```go
// 获取用户列表
func GetUsers() ([]User, error) {
    var users []User
    sql := fmt.Sprintf("SELECT * FROM users WHERE deleted_at IS NULL")
    if err := db.Select(&users, sql); err != nil {
        return nil, err
    }
    return users, nil
}

// 指定 id 获取用户
func GetUser(id: int) (User, error) {
    var user User
    sql := fmt.Sprintf("SELECT * FROM users WHERE id = $1 AND deleted_at IS NULL")
    if err := db.Get(&user, sql, id); err != nil {
        return nil, err
    }
    return user, nil
}

// 插入单个用户
func InsertUser(u User) error {
    sql := "INSERT INTO users (email, username, password) VALUES (:email, :username, :password)"
    result, err := db.NamedExec(sql, u)
    if err != nil {
        return err
    }

    count, err := result.RowsAffected()
    if err != nil {
        return err
    }
    return nil
}

// 更新单个用户
func UpdateUser(u User) error {
    sql := "UPDATE users SET email = :email, username = :username, password = :password WHERE id = :id"
    result, err := db.NamedExec(sql, u)
    if err != nil {
        return err
    }

    count, err := result.RowsAffected()
    if err != nil {
        return err
    }
    return nil
}
```

## 自定义封装

```go
package db

import (
    "fmt"
    "log"
    "sync"
    "time"

    sql "github.com/jmoiron/sqlx"
    _ "github.com/lib/pq"
)

type DBConn struct {
    Host     string
    Port     int
    Username string
    Password string
    DBName   string
}

type DBName string

const (
    Base   DBName = "base"
    Backup DBName = "backup"
)

var ConnMap = map[DBName]*DBConn{
    Base: {
        Host:     "localhost",
        Port:     5432,
        Username: "username",
        Password: "password",
        DBName:   "dbname",
    },
}
var (
    DBMap     = make(map[DBName]*sql.DB, len(ConnMap))
    defaultDB *sql.DB
    mutex     sync.RWMutex
)

// init all database connection
func Init() {
    for name, conn := range ConnMap {
        cursor, err := sql.Connect(
            "postgres",
            fmt.Sprintf("postgres://%s:%s@%s:%d/%s?sslmode=disable",
                conn.Username, conn.Password, conn.Host, conn.Port, conn.DBName,
            ))
        if err != nil {
            log.Fatalf("connect database %s err: %v", conn.DBName, err)
        }
        cursor.SetMaxOpenConns(10)
        cursor.SetMaxIdleConns(5)
        cursor.SetConnMaxLifetime(5 * time.Minute)
        DBMap[name] = cursor
    }

    defaultDB = DBMap[Base]
}

// get default database
func New() *sql.DB {
    mutex.RLock()
    defer mutex.RUnlock()
    return defaultDB
}

// get non default database
func Temp(name DBName) *sql.DB {
    mutex.RLock()
    defer mutex.RUnlock()
    return DBMap[name]
}

// set default database
func SetDefault(name DBName) {
    mutex.Lock()
    defer mutex.Unlock()
    defaultDB = DBMap[name]
}

// Close all database connection
func Close() {
    mutex.Lock()
    defer mutex.Unlock()
    for _, db := range DBMap {
        if db == nil {
            continue
        }
        db.Close()
    }
}
```

使用示例

```go
package main

func main() {
    var users []User
    err := db.New().Select(&users, "SELECT * FROM users WHERE deleted_at IS NULL")
    if err != nil {
        log.Fatalf("select users err: %v", err)
    }
}
```
