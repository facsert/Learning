---
pubDatetime: 2025-03-19 10:56:12
modDatetime:
title: 07.Bash Script
slug: 07.Bash Script
featured: false
draft: false
tags:
  - bash
description: "Bash Script"
---

## 脚本介绍

脚本（script）就是包含一系列命令的一个文本文件。Shell 读取这个文件，依次执行里面的所有命令  
脚本的好处是可以重复使用，也可以指定在特定场合自动调用，比如系统启动或关闭时自动执行脚本

linux 和 macos 可以直接执行 Shell 脚本(.sh 文件), windows 使用 cmd (.bat 文件)和 powershell(.ps1 文件)

```bash
# 编辑 text.sh
echo "my first script"

 # 执行 脚本(指定 shell 执行 `bash test.sh`)
 $ ./test.sh
 > my first script
```

shell 使用 `#` 表示注释, 注释后面的内容不会执行

```bash
# 编辑 text.sh
echo "test script commit"
# echo "commit line"

 $ ./test.sh
 > test script commit
```

## 脚本参数

`getopts` 可以用于解析脚本参数, 单该命令只能解析单字母参数  
通过 `while` 循环和 `shift`(移除第一个参数, 后续参数向前一位) 读取所有参数

```bash
usage=$(
  cat <<EOF
  -h/--help      show help   \n
  -t/--test      script test \n
  -d/--dir DIR   set workdir \n
  --host HOST    set host    \n
EOF
)

TEST=false
WORKDIR="/home"
HOST="192.168.1.100"

while [[ $# -gt 0 ]]; do
  # 获取第一个参数进行匹配
  case $1 in
  # 匹配到 -h 或 --help 打印 usage 内容, 并退出
  -h | --help)
    echo -e $usage
    exit 0
    ;;
  # 匹配到 -t 或 --test 执行命令, 并移除第一个参数
  -t | --test)
    echo "Set test flag to true"
    Test=true
    shift 1
    ;;
  # 匹配到 -d 或 --dir 执行命令, 并移除前两个参数
  -d | --dir)
    echo "Set workdir to $2"
    WORKDIR=$2
    shift 2
    ;;
  --host)
    echo "Set host to $2"
    HOST=$2
    shift 2
    ;;
  *)
    echo "error param"
    shift
    ;;
  esac
done
```

```bash
 $ bash test.sh -t
 > Set test flag to true

 $ bash test.sh --help
 > -h/--help show help 
 > -t/--test script test 
 > -d/--dir DIR set workdir 
 > --host HOST set host 
```

## 引入模块

使用 `source` 命令加载外部模块  
`source script.sh` 表示在当前 shell 执行 script.sh 脚本, script.sh 定义的函数变量便加载到当前 shell  
`./script.sh` 或 `bash script.sh` 是创建一个子 shell 执行脚本, 当前 shell 未加载脚本内函数或变量

```bash
# module.sh 定义
REPEAT_TIMES=2

function repeat() {
  local old=$1
  local new=$1
  for((i=0; i<$REPEAT_TIMES; i++)) {
    new="${new}_${old}"
  }
  echo $new
}

echo "module finished"
```

```bash
# main.sh 引入 module.sh 定义的变量和函数
source ./module.sh

echo $(repeat "*")
REPEAT_TIMES=5
echo $(repeat "*")
```

由于 source 本质是执行脚本, 模块执行代码也会被执行

```bash
 $ bash test.sh
 > module finished
 > *_*_*
 > *_*_*_*_*_*
```

## read

read 命令用于读取输入, 如命令行输入, 文件

```bash
# read 参数 变量, 支持输入多个值写入多个变量
read [-options] [variable...]

echo -n "input some string > "
read first second
echo "your input is $first $second"
 
 # read 设置变量少于输入, 多余值全放入最后一个变量
 # first == "1st"  second == "2nd 3rd"
 $ bash input.sh
 > input some string > 1st 2nd 3rd
 > your input is 1st 2nd 3rd
```

```bash
echo -n "input some string > "
read
echo "your input is $REPLY"
 
 # read 不设置变量, 所有输入保存到 REPLY
 # REPLY == "1st 2nd 3rd"
 $ bash input.sh
 > input some string > 1st 2nd 3rd
 > your input is 1st 2nd 3rd
```

### read 参数

```bash
# -t 设置等待输入超时时间, 单位秒
echo -n "input > "
if read -t 3 input; then
  echo "input: $input"
else
  echo "timeout"; return 1
fi

# -p 设置输入提示
read -p "input > "
echo "input: $REPLY"

# -e 允许输入时使用快捷键, 如补全, 方向键
read -ep "input > " input
echo "input: $input"

# -d "$" 更换输入结束符号为 $(默认为换行符)
read -p "input >" -d "$" a
echo -e "\ninput: $a"

 $ bash input.sh
 > input >1st
 > 2nd
 > 3rd$
 > input: 1st
 > 2nd
 > 3rd

# -s 隐藏输入, 常用于密码输入
# -r raw 模式, 不转移输入内容
# -n <num> 只读取指定数量的字符写入变量
```

### 文件读取

read 读取输入默认以空格作为分隔符, 使用 `IFS` 变更分隔符

```bash
# IFS 设置和 read 同一行, 仅在 read 命令生效
IFS="," read -p "input >" a b c
echo -e "input: a=$a b=$b c=$c"

input >1,2,3
input: a=1 b=2 c=3
```

```bash
# 文件内容逐行重定向到 read 命令
# IFS 分隔符设置为空, 避免行首行尾空格分隔
# -r 避免字符转义
while IFS= read -r line; then
    echo "line: $line"
done < file.txt

while IFS= read -r line; do
    # 跳过空行
    if [[ -z "$line" ]]; then
        continue
    fi
    # 跳过以 # 开头的注释行
    if [[ "$line" =~ ^# ]]; then
        continue
    fi
    echo "Valid Line: $line"
done < config.txt
```
