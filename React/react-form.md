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
import { useForm } from "react-hook-form"
import { z } from "zod"
import { zodResolver } from "@hookform/resolvers/zod"

// 输入表单的约束规则
const formSchema = z.object({
    title: z.string().min(2, {
        message: "todo title must be at least 2 characters.",
    }),
    content: z.string().min(2, {
        message: "todo content must be at least 2 characters.",
    }),
})

function Form() {
    // 设置表单的初始值
    const form = useForm<z.infer<typeof formSchema>>({
        resolver: zodResolver(formSchema),
        defaultValues: { title: "", content: ""},
    })
    
    // 提交表单操作
    const onSubmit = (data: z.infer<typeof formSchema>) => {
        console.log(data.title)
        console.log(data.content)
    }

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
                        <FormDescription>
                            This is your Todo title
                        </FormDescription>
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
                        <FormDescription>
                            This is your Todo content
                        </FormDescription>
                    </FormItem>
                    )}
                />
                <DialogClose type="submit">
                    ADD
                </DialogClose>
            </form>
        </Form>
    )
}
```
