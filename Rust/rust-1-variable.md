---
pubDatetime: 2025-08-07 09:29:19
title: 01.Go 
featured: false
draft: false
tags:
  - Rust
description: "Rust 基础"
---

## 介绍

- 标量（scalar）：整数（signed/unsigned）、浮点数、布尔（bool）、字符（char）
- 复合（compound）：元组（tuple）、数组（array）、切片（slice）
- 字符串类：`&str`（字符串切片）、`String`
- 引用与指针：`&T`, `&mut T`, `*const T`, `*mut T`
- 常用包装/错误类型：`Option<T>`、`Result<T, E>`
- 其它常见 trait/特性：Copy / Clone、Debug / Display、From/Into、AsRef/AsMut

[RUST 标准库基本类型](https://wangchujiang.com/rust-cn-document-for-docker/std/std/all.html#primitives)

## 整数

有符号(正/负整数): i8, i16, i32, i64, i128, isize
无符号(正整数): u8, u16, u32, u64, u128, usize

类型范围  
i32 => -2^31 ~ 2^31-1, u32 0 ~ 2^32
isize 范围取决于机器平台, 32 位操作系统 isize = i32, 64 位操作系统, isize = i64

i32 是绝大多数场景整数默认类型, 如类型推断 `let n = 5`, 不显示声明 n 默认为 i32 类型

```rs
let n8: i8 = 8; // 前置显式标注类型
let n16 = 16_i16; // 后置显式标注类型
let n32 = 32; // 使用类型推断默认类型
let n64 = 64 as i64; // 使用类型推断并强制转换类型
let nsize: isize = isize::MAX; // isize 在 64 位系统等同 i64

println!("{} is {}", n8, type_name(&n8));
println!("{} is {}", n16, type_name(&n16));
println!("{} is {}", n32, type_name(&n32));
println!("{} is {}", n64, type_name(&n64));
println!("{} is {}, isize::MAX = 2^63-1 is {}", nsize, type_name(&nsize), nsize == 2 * (2_isize.pow(62) - 1) + 1);


fn type_name<T>(_: &T) -> &'static str {
    return std::any::type_name::<T>()
}

8 is i8
16 is i16
32 is i32
64 is i64
9223372036854775807 is isize, isize::MAX = 2^64-1 is true
```

不同类型不能比较大小, 需转换成同一类型才可以(不同类型转换可能存在溢出)

```rs
println!("n8 < n16 is {}", n8 < n16) // 报错
39 |     println!("n8 < n16 is {}", n8 < n16)
   |                                --   ^^^ expected `i8`, found `i16`
   |                                |
   |                                expected because this is `i8`
   |
help: you can convert `n8` from `i8` to `i16`, matching the type of `n16`
   |
39 |     println!("n8 < n16 is {}", i16::from(n8) < n16)
   |                                ++++++++++  +

println!("n8 < n16 is {}", i16::from(n8) < n16) // n8 转为 i6 类型
n8 < n16 is true
```

常用整数属性或方法

```rs
i32::MAX // i32 类型的最大值(2^31-1)
i32::MIN // i32 类型最小值(-2^31)

let n:i32 = i32::from(3_i8); // 转换成 i32 类型
println!("n is {}", n); 
println!("n.abs() is {}", n.abs()); // 获取绝对值
println!("n.pow(3) is {}", n.pow(3)); // 幂运算
println!("n.is_positive() is {}", n.is_positive()); // 判断是否正数
println!("n.is_negative() is {}", n.is_negative()); // 判断是否正数

n is 3
n.abs() is 3
n.pow(3) is 27
n.is_positive() is true
n.is_negative() is false
```

## 浮点数

f32, f64

浮点数默认为 f64

```rs
let inf = f64::INFINITY;

let f = 3.1415;
f.floor(); // 3.0 向下取整
f.ceil(); // 4.0 向上取整
f.round(); // 3.0 四舍五入
f.is_nan(); // false 判断是否为 nan
f.is_infinite(); // false 判断为正无穷或负无穷

f.max(1.414); // 3.1415 与 1.414 比较，返回更大的浮点数
f.min(1.414); // 1.414 与 1.414 比较，返回更小的浮点数
```

## 布尔值

布尔类型(占用 1 byte): true, false  

```rs
let t = true; // 布尔类型不支持后缀类型(true_bool 会报错)
let f: bool = false;
```

## 字符

所有 Unicode 值都可以作为 RUST 字符, 单个字符占用 4 个字节  
所有 char 类型都可以转为 u32  
0 到 0x10FFFF u32 数值可以转为 char  
char 类型必须使用单引号且字符数量为 1, 如 'A', '字'， ' ', 空字符 '' 不是 char 类型

```rs
for i in 'a'..='z' {
    print!("{i}");
}
abcdefghijklmnopqrstuvwxyz

println!("ascii 67 is {:?}", char::from_u32(65_u32).unwrap());
println!("char B to u32 {}", 'B' as u32);

ascii 67 is 'A'
char B to u32 66
```

## 字符串

RUST 引入所有权和借用的概念, 每个一个值只能被一个变量拥有  
部分类型数据赋值为了满足值只能被一个变量拥有, 会同时将所有权一同转移, 原有变量赋值后无法使用  
部分类型赋值时通过 Copy 赋值不会转移所有权, 原有变量仍然可用

Copy 类型有: 整数, 浮点数, 布尔值, 字符  
非 Copy 类型带有传染性, 复杂类型只要包含就会变成非 Copy 类型

```rs
let m: String = String::from("owner"); // 创建 String 类型字符串(可变长，所有权可变)
let n = m; // m 的值和所有权传递给 n, m 不再可用
println!("check m {}", m);

println!("check m {}", m);
                       ^ value borrowed here after move
```

字符串有两种

- String: 可变长度, 拥有所有权, 在堆上创建, 速度较慢
- &str: 字符串默认类型, 无所有权, 不可变, 在栈中, 速度快

```rs
let s = "static str" // 创建 &str 类型

```

## 元组

元组是一个若干不同类型的值打包而成, 生成后数量不可变, 可使用序号取值

```rs
// 创建元组并自行推断类型 (u8, u16, u32)
let tup = (8_u8, 16_u16, 32_u32);
println!("tuple is {:?}", tup);

tuple is (8, 16, 32)

// 通过元组解包赋值, 元组使用序号取值
let (x, y, z) = (tup.0, tup.1, tup.2);
println!("x:{x} y:{y} z:{z}");

x:8 y:16 z:32

// 使用元组返回多个值
let (a, b, c) = return_tuple();
fn return_tuple() -> (u8, u16, u32) {
    return (8_u8, 16_u16, 32_u32)
}

// 数组转为元组
let array: [i32; 3] = [1,2,3];
let tup: (i32, i32, i32) = array.into();
```

## 数组

- 长度固定
- 数组所有元素类型相同
- 有固定顺序, 线性排列

```rs
let list1 = [1,2,3]; // 类型推断 类型 i32, 长度为 3
let list2: [i32; 3] = [1,2,3]; // 与 list1 完全一致
let list3 = [8_i8; 4]; // 快速生成 i8 类型, 长度为 4 数组 [8,8,8,8]

// 元组转数组
let tuple: (i32, i32, i32) = (1, 2, 3);
let array: [i32; 3] = tuple.into();
println!("tuple transf list {:?}", array);

list1.len()

// 非 Copy 类型数组直接遍历会消耗元素所有权
let array = [String::from("John"), String::from("Lily")];
for i in array {
    println!("value: {}", i); // 会消耗元素所有权, array 遍历后不可用
}
println!("array: {:?}", array);

println!("array: {:?}", array);
                        ^^^^^ value borrowed here after move

// 使用 iter() 遍历不消耗使用权
for i in array.iter() {
    println!("value: {}", i); // 不消耗元素所有权, array 遍历后仍可用
}
println!("array: {:?}", array);

value: John
value: Lily
array: ["John", "Lily"]
```
