---
pubDatetime: 2024-01-20 11:33:44
title: 01.Powershell Basic
slug: 01.Powershell Basic
featured: false
draft: false
tags:
  - powershell
description: "powershell 基本语法"
---

Powershell 是 windows 上推荐的 shell 脚本语言

## Table of Contents

## 介绍

[PowerShell 官方文档](https://learn.microsoft.com/zh-cn/powershell/)

```powershell
# 允许任意 powershell 脚本执行
Set-ExecutionPolicy Unrestricted

# 计算机管理=>服务=>启动 OpenSSH(外部 SSH 进入 windows 默认 shell 设为 powershell)
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force

# 查找命令, 支持通配符
Get-Command -Name *Process

# 查看命令如何使用, 参数, 别名(使用 -detailed 或 -Full 参数显示详细, 推荐 Update-Help 更新 Help 文档)
Get-Help Get-Process
help Get-Process
```

powershell 使用 `# commit` `<# commit #>` 作为单行和多行注释

```powershell
# powershell 单行注释

<#
  powershell 多行注释
#>
```

powershell 使用反引号作为转义符号

| 符号 | \`n  | \`r | \`t |
| :--: | :--: | :-: | :-: |
| 含义 | 换行 |     |     |

输出重定向

| 符号 |   `>`    |   `>>`   |     `>&1`      | `>null`  |
| :--: | :------: | :------: | :------------: | :------: |
| 含义 | 覆盖写入 | 追加写入 | 输出到标准输出 | 输出置空 |

- 1: 成功流
- 2: 错误流
- 3: 警告流
- \*: 所有流

```powershell
command n>file

Get-Process -Name python 2>&1
```

## 基础

powershell 不区分大小写, 使用 tab 可以补全命令

```powershell
#

# 显示路径下的文件和文件夹, 类似 ls
Get-ChildItem 别名: ls, dir, gci

# 切换路径, 类似 cd
Set-Location 别名: cd, chdir, sl

# 打印内容, 类似 echo
Write-Output 别名: echo, write

# 读取文件内容(使用 findstr 筛选), 类似 cat
Get-Content 别名: cat, gc, type
cat text.log | findstr key

# 创建文件或路径
New-Item 别名: ni(默认创建文件, 支持 mkdir 创建路径), md
mkdir folder(ni -ItemType Directory folder)

# 复制, 类似 cp
Copy-Item 别名: cp, copy, cpi

# 移动, 类似 mv
Move-Item 别名: mv, move, mi

# 删除, 类似 rm
Remove-Item 别名: rm, rmdir, del, erase, rd, ri

# 查看指定过的命令, 类似 history
Get-History 别名: history, ghy, h

# 查看进程列表, 类似于 ps
Get-Process 别名: ps, gps

# 行筛选
Select-String 别名: sls, findstr
Where-Object 别名: where
ls | findstr "text.txt"
ls | where name -match "txt"
```

```powershell
# 通过进程名称匹配获取进程对象
Get-Process -Name "*python*"
Get-Process -Name "*python*"

# 显示进程对象的所有属性
Get-Process -Name "*python*" | Get-Member

# 根据属性名称获取进程对象属性值
Get-Process -Name "*python*" | select <key name>

Get-Process -Name "*python*" | select ProcessId
Get-Process -Name "*python*" | select StartTime
Get-Process -Name "*python*" | select Path

# 类似于上述方式, 但获取的进程对象属性有差异
Get-CimInstance Win32_Process | where Name -match "python"

# 获取进程执行命令
Get-CimInstance Win32_Process | where Name -match "python" | select CommandLine
Get-CimInstance Win32_Process | where Name -match "python" | select Path
```

## 变量

powershell 以 `$` 开头定义变量, 变量是强类型, 对大小写不敏感

```powershell
$str = "a string variable"                       # 定义字符串
$num = 3                                         # 定义数值
$arry = 1,2,3,4                                  # 定义列表
$list = @()                                      # 定义空列表
$hash = @{name="John"; age=18}                   # 定义哈希表

[string]$str = "hello"
[int32]$num = 10
[Object[]]$arr = 3,4,5
[Hashtable]$map = @{a=1; b=2}
```

```powershell
$variable = "hello"
$variable.GetType()                              # 获取变量类型

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object

$variable | Get-Member                           # 查看变量的属性和方法

   TypeName:System.String

Name             MemberType            Definition
----             ----------            ----------
Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int I...
Contains         Method                bool Contains(string value)
......
......
```

## 数组

```powershell
$empty = @()
$list = 0,1,2,3,4
$array = 0..4

$list[0]
$list[1..3]
$list[-1]

$list.Length
```

## 哈希表

```powershell
$empty = @{}
$hash = @{name="John"; age=18}
```

## 控制语句

```powershell
if ($a -gt $b) {
    # code
} else {
    # code
}

while ($a -eq $b) {
    # code
}

for ($i = 0; $i -lt 10; $i++) {
    # code
}

foreach ($i in $list) {
    # code
}

switch ($value) {
  1 { <# code #>}
  2 { <# code #>}
  3 { <# code #>}
  default { <# code #>}
}
```

### 比较运算符

```powershell
3 -eq 3                                          # True 数字或字符串相等(-ceq 区分大小写)
2 -ne 3                                          # True 数字或字符串不等
3 -gt 2                                          # True 大于
3 -ge 2                                          # True 大于等于
3 -lt 2                                          # False 小于
3 -le 2                                          # False 小于等于

1,2,3 -Contains 2                                # True 列表包含(NotContains)
2 -in 1,2,3                                      # True 列表包含(NotIn)
"aab" -Match "a"                                 # True 字符串包含, 支持正则表达式(NotMatch)
"aab" -Like "a*"                                 # True 相似匹配(NotLike)

!2                                               # False, 取反
1 -and 2                                         # True, 与
1 -or 0                                          # True, 或
-not 1                                           # True, 非
```

## 函数

```powershell
function myfunc($param1, $param2) {
    # code
}

function max([int]$a, [int]$b) {
    if ( $a -ge $b ) {
        return $a
    } else {
        return $b
    }
}

echo "max num: $(max 5 3)"

# 官方函数定义
function Max-Num {
    Param (
        [int] $M,
        [int] $N
    )

    if ( $M -ge $N ) {
        return $M
    } else {
        return $N
    }
}

echo "max num: $(Max-Num -M 3 -N 5)"
```

## 特定变量

```powershell
$?                                               # 上一条命令执行结果
$false                                           # False
$true                                            # True
$null                                            # Null
$PID                                             # 进程ID
$PWD                                             # 当前目录
```
