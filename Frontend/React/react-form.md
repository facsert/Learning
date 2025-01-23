---
author: facsert
pubDatetime: 2024-04-29 22:39:27
title: React form
slug: React form
featured: false
draft: false
tags:
  - React
description: "React 表格组件"
---

## Table of Contents

```js
"use client"

import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Button } from "@/components/ui/button";
import { FormEvent, useState, ChangeEvent } from "react";

class Client {
  constructor(
    public host: string = "192.168.1.100",
    public port: number = 22,
    public username: string = "root",
    public password: string = "admin"
  ) {}
}

export default function FormPage() {
    const [client, setClient] = useState<Client>(new Client())

    const handleSubmit = async (event: FormEvent<HTMLFormElement>) {
        event.preventDefault();
        console.log(JSON.stringify(client))
    }

    const handleAction = (formData: FormData) => {
        const data = Object.fromEntries(formData)
        setClient(data as Client)
    }

    const handleChange = (event: ChangeEvent<HTMLInputElement>) => {
      // 获取 Input 组件的 name 和 value 属性
      const {name, value} = event.target as HTMLInputElement;
      setClient({...client, [name]: value})
    }

    return (
      <form action={handleAction}>
      {/* <form onSubmit={handleSubmit}> */}
        <Label htmlFor="host">host</Label>
        <Input name="host" type="text" id="host" value={client.host} onChange={handleChange}/>

        <Label htmlFor="port">port</Label>
        <Input name="port" type="number" id="port" value={client.port} onChange={handleChange} />

        <Label htmlFor="username">username</Label>
        <Input name="username" type="text" id="username" value={client.username} onChange={handleChange} />

        <Label htmlFor="password">password</Label>
        <Input name="password" type="text" id="password" value={client.password} onChange={handleChange} />

        <Button type="submit">Submit</Button>
        <Button type="button" onClick={() => setClient(new Client())}>Reset</Button>
      </form>
    );
}
```

```js
import { useForm } from "react-hook-form";
import { z } from "zod";
import { zodResolver } from "@hookform/resolvers/zod";

// 输入表单的约束规则
const formSchema = z.object({
  title: z.string().min(2, {
    message: "todo title must be at least 2 characters.",
  }),
  content: z.string().min(2, {
    message: "todo content must be at least 2 characters.",
  }),
});

function Form() {
  // 设置表单的初始值
  const form =
    useForm <
    z.infer <
    typeof formSchema >>
      {
        resolver: zodResolver(formSchema),
        defaultValues: { title: "", content: "" },
      };

  // 提交表单操作
  const onSubmit = (data: z.infer<typeof formSchema>) => {
    console.log(data.title);
    console.log(data.content);
  };

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">

        <FormField
          control={form.control}
          name="title"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Title</FormLabel>
              <FormControl>
                <Input placeholder="Write todo here" {...field} />
              </FormControl>
              <FormDescription>This is your Todo title</FormDescription>
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="content"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Content</FormLabel>
              <FormControl>
                <Textarea placeholder="Write todo here" {...field} />
              </FormControl>
              <FormDescription>This is your Todo content</FormDescription>
            </FormItem>
          )}
        />

        <Button type="submit">ADD</Button>
      </form>
    </Form>
  );
}
```
