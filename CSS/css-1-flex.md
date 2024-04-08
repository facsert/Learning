---
author: facsert
pubDatetime: 2024-03-05 16:11:43
title: CSS flex
slug: CSS flex
featured: false
draft: false
tags:
  - CSS
description: "CSS flex 布局"
---

## Table of Contents

## 介绍

flex 浮动布局是一种强大的布局方式, 用于创建灵活的、响应式的页面布局  
Flex 布局模型提供了一种更加简单, 高效的方式来布局, 对齐, 排序和分布容器中的子元素

## 属性

浮动布局

[FreeCodeCamp CSS Flexbox](https://www.freecodecamp.org/chinese/news/the-css-flexbox-handbook/#the-align-content-property)

主轴方向为 flex-direction 的值, 值为 row, 水平方向为主轴

```css
.contain {
    /* 设置组件为块级组件并使用浮动布局 */
    display: flex;

    /* 定义组件的主轴, 横向左至右, 横向右至左, 纵向上至下, 纵向下至上  */
    flex-direction: row|row-reverse|column|column-reverse;
    
    /* 沿主轴方向排列方式, 主轴起点|主轴终点|主轴中心|主轴均匀分布, 首尾无间隙|主轴均匀分布, 首尾有间隙  */
    justify-content: flex-start|flex-end|center|space-between|space-around;
    
    /* 垂直主轴方向排列方式(交叉轴), 拉伸(默认, 拉伸子元素填充)|交叉轴起点|交叉轴终点|交叉轴中心|子元素基线对齐 */
    align-items: stretch| flex-start|flex-end|center|baseline ;
    
    /* 多行 flex 容器, 拉伸(默认, 拉伸子元素填充)|交叉轴起点|交叉轴起点终点|交叉轴中心|交叉轴均匀分布(无间隙)|交叉轴均匀分布(有间隙) */
    align-content: stretch| flex-start|flex-end|center|space-between|space-around ;

    /* 当主轴空间不足容纳子元素时操作, 不换行(溢出), 换行, 换行并反向排列 */
    flex-wrap: nowrap|wrap|wrap-reverse;
}
```
