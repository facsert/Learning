---
author: facsert
pubDatetime: 2024-03-05 17:18:43
title: HTML Base
slug: HTML Base
featured: false
draft: false
tags:
  - HTML
description: "HTML 基础"
---

## Table of Contents

## 介绍

HTML(Hypertext Markup Language) 翻译为超文件标记语言, 用于描述网页的结构和内容

- 语义化: HTML 标签具有语义化, 标签名和作用紧密相关
- 层级结构: HTML 标签具有层级结构, 通过嵌套关系描述网页结构
- 可拓展性: HTML 可通过自定义属性拓展网页内容

```html
<!DOCTYPE html>
<!-- 声明文档类型 -->
<html lang="en">
  <!-- 文档根元素 -->
  <head>
    <!-- 文档头部, 包含了元数据和引用的外部资源等信息 -->
    <meta charset="UTF-8" />
    <!-- 字符编码 -->
    <title>HTML5 页面示例</title>
    <!-- 浏览器标题栏内容 -->
  </head>

  <body>
    <!-- 主体可视部分 -->
    <header>
      <!-- 页眉, 包含标题导航等 -->
      <h1>欢迎来到我的网站</h1>
      <nav>
        <ul>
          <li><a href="#">首页</a></li>
          <li><a href="#">关于我们</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <!-- 页面主要内容 -->
      <article>
        <h2>最新文章</h2>
        <p>这是一篇关于HTML5的文章...</p>
      </article>
    </main>

    <footer>
      <!-- 页脚, 版权信息, 外部链接 -->
      <p>&copy; 2024 我的网站</p>
    </footer>
  </body>
</html>
```

## 全局属性

全局属性 (global attributes) 是所有元素都可以使用的属性(有些属性对部分元素无意义)

```html
<!-- id class 均用于选择器定位元素  -->
<div id="id id-name" class="div-class div-name">select html</div>

<!-- title, 鼠标悬停在元素时可见 title 内容 -->
<div title="visible when mouse hover">mouse hover here</div>

<!-- style 可定义 html 内联样式 -->
<div style="color:red;">css in html</div>
```

## 语意标签

## head

head 用以描述文件的标题, 关键字, 字符编码, 引入外部资源, 通过页面元数据(描述和概括能)可更容易找到相关信息

```html
<head>
  <meta charset="UTF-8" />
  <!-- 设置字符编码为UTF-8 -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <!-- 设置移动设备视口 -->
  <meta name="description" content="web site" />
  <!-- 设置页面描述 -->
  <title>web title</title>
  <!-- 设置页面标题 -->
  <link rel="stylesheet" href="styles.css" />
  <!-- 引入外部 CSS 样式 -->
</head>
```

## 文本标签

```html
<p>text 文本标签</p>
<i>italic 斜体</i>
<b>bold 加粗</b>
<u>underline 下划线</u>
<s>delete 删除线</s>
```

## 表格

```html
<!-- 有序列表 -->
<ol>
  <li>line 1</li>
  <li>line 2</li>
  <li>line 3</li>
</ol>

<!-- 无序列表 -->
<ul>
  <li>line 1</li>
  <li>line 2</li>
  <li>line 3</li>
</ul>

<!-- 定义术语列表, dt 术语名, dd 术语解释 -->
<dl>
  <dt>name</dt>
  <dd>John</dd>

  <dt>age</dt>
  <dd>20</dd>
</dl>
```

```html
<!-- table 表格(边框 2px) -->
<table border="2px">
  <!-- tr table row, 一行 -->
  <tr>
    <!-- th table header, 表头 -->
    <th>id</th>
    <th>name</th>
    <th>age</th>
  </tr>
  <tr>
    <!-- td table data, 表格数据 -->
    <td>1</td>
    <td>John</td>
    <td>20</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Jack</td>
    <td>21</td>
  </tr>
</table>
```
