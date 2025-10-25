---
author: facsert
pubDatetime: 2025-10-24 15:39:27
title: React component
slug: React component
featured: false
draft: false
tags:
  - React
description: "React 基础组件"
---


## common

react-dom 基础组件

```tsx
<div
  id="index"             // string 唯一标识符, 不能重复
  className="bg-blue"    // string 类名, 通常用于绑定 CSS 样式

  title="hover"          // string 鼠标悬停时显示内容

  onClick={() => void}   // 组件点击事件
  onKeyDown={() => void} // 键盘事件
  
  hidden={true}          // 是否隐藏组件
>
  react component
</div>
```

参数传递

```tsx
// JS 支持解包, 如 const { name, value } = props;
// 参数可直接写成多个变量组成的对象
// children 是特殊参数，特指一个可被包含的组件
// props 为可变参数, 通常用于传递组件属性
function TextCard(
  {
    title,
    children,
    ...props
  }:{
    title: string;
    children: React.ReactNode;
  }
) {
  return (
    <div title={title} {...props}>
      {children}
    </div>
  );
}
```

## input

```tsx
// input 属性
// type 输入框类型(text number password email)
// defaultValue 默认值
// value 输入框的值
// onChange  值变更事件
// placeholder 无输入时显示内容
// disable  禁用输入
// maxLength 最大输入长度

// 一般常用 useState 监控(onChange)和控制输入框的值(value)

interface InputData {
  text: string;
  number: number;
  password: string;
  email: string;
}

const newInputData = (): InputData => {
  return {
    text: "",
    number: 1,
    password: "",
    email: "",
  }
}

export default function MultiInput() {
  const [data, setData] = useState<InputData>(newInputData())
  const handleChange = (event: ChangeEvent<HTMLInputElement>) => {
    //input 组件 name 属性与 data 键同名便于对应赋值
    const {name, value} = event.target
    setData({...data, [name]: value})
  }
  return (
    <div>
      <div>
        <Label htmlFor="1"></Label>
        <Input
          id="1"
          type="text"
          name="text"  
          defaultValue={"empty"}
          placeholder="input text"
          value={data.text}
          onChange={handleChange}
        />
      </div>

      <div>
        <Label htmlFor="2"></Label>
        <Input
          id="2"
          type="number"
          name="number"
          defaultValue={1}
          placeholder="input number"
          value={data.number}
          onChange={handleChange}
        />
      </div>

      <div>
        <Label htmlFor="3"></Label>
        <Input
          id="3"
          type="password"
          name="password"
          defaultValue={"abcd"}
          placeholder="input password"
          value={data.password}
          onChange={handleChange}
        />
      </div>

      <div>
        <Label htmlFor="4"></Label>
        <Input
          id="4"
          type="email"
          name="email"
          defaultValue={"example@mail.com"}
          placeholder="input email"
          value={data.email}
          onChange={handleChange}
        />
      </div>
    </div>
  )
}

// input 复选框
function Checkbox() {
  const [hobbies, setHobbies] = useState<string[]>([])
  const handleChange = (hobby: string) => {
    setHobbies(pre => 
      pre.includes(hobby)
        ? pre.filter(h => h !== hobby)
        : [...pre, hobby]
    )
  }
  return (
    <div>
      <div>
        <Checkbox
          checked={hobbies.includes("music")}
          onChange={() => handleChange("music")}
        />
        <Label>Music</Label>
      </div>
      <div>
        <Checkbox
          checked={hobbies.includes("read")}
          onChange={() => handleChange("read")}
        />
        <Label>Read</Label>
      </div>
    </div>
  )
}
```

## select

select 是多选框

```tsx
function SelectBox() {
  const [location, setLocation] = useState("ShangHai")
  return (
    <div>
      <select
        defaultValue={location}
        value={location}
        onChange={(e) => setLocation(e.target.value)}
      >
        <option value="Beijing">北京</option>
        <option value="ShangHai">上海</option>
        <option value="GuangZhou">广州</option>
      </select>
    </div>
  )
}
```
