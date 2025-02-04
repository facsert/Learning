---
pubDatetime: 2023-03-23 15:28:43
title: NodeJS variable
slug: NodeJS variable
featured: false
draft: false
tags:
  - NodeJS
description: "NodeJS 变量"
---

JS 是动态语言, 变量可以随意变换类型  
JS 使用 `let`, `var`, `const` 声明变量

## 变量声明

```js
let a = "hello";
var b = " ";
const c = "world!";
var b;

console.log(a + b + c)
> hello world!
```

- `var`: 声明全局变量, 可以重复声明, 重复声明不赋值会继承上一个值
- `let`: 声明区域变量, 不可重复声明
- `const`: 声明常量, 值不可变, 声明时必须赋值, 不可重复声明

```js
// var 存在变量提升, 变量在声明前使用为 undefined
console.log(a);
var a = "hello";
> undefined

// var 声明的是全局变量
for(var i=0; i < 5; i++) {
    break
}

// let 声明的变量只在一定范围内生效
for(let x=0; x < 5; x++) {
    break
}

console.log(`index i: ${i}`);
try {
    console.log(`index x: ${x}`);
} catch (error) {
    console.log(error);
}

> index i: 0
> ReferenceError: x is not defined
```

注: 为避免 `var` 变量提升和全局声明, 推荐使用 `let`

```js
const MAX;
> SyntaxError: Missing initializer in const declaration

// 声明常量, 且需要赋值
const MAX = 100;

MAX = 200;
> TypeError: Assignment to constant variable.
```

## 特殊全局变量

```js
// 代码所在文件的绝对路径
console.log(__dirname);
console.log(__filename);

// process 对象, 进程信息
console.log(process.argv); // Array 执行脚本命令及参数
console.log(process.pid); // number 进程 pid
console.log(process.platform); // string 执行平台
console.log(process.uptime()); // number 执行时长
console.log(process.cwd()); // string 入口文件路径
console.log(process.env); // Object shell 下的环境变量(linux env 命令)
```
