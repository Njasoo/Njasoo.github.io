---
title: 学习day22
date: 2025-12-05 10:24:15
tags: ["react", "next"]
---

# form action

```html
<form action="/api/login" method="post"></form>
```

按照普通的 html, form 的 action 属性就是在表单提交之后, 放一个路由跳转到处理逻辑的接口地址

```tsx
// Server Component
export default function Page() {
  // Action
  async function create(formData: FormData) {
    "use server";

    // Logic to mutate data...
  }

  // Invoke the action using the "action" attribute
  return <form action={create}>...</form>;
}
```

# console.log 在服务端和客户端的区别

首先说一下如果一个文件顶部写了"use server"的话, 那么里面所有的函数都默认带上了 use server

在服务端的 log 会打印到控制台, 客户端的会打印到浏览器

# zod 数据类型校验

```tsx
import { z } from "zod";

const FormSchema = z.object({
  id: z.string(),
  customerId: z.string(),
  amount: z.coerce.number(),
  status: z.enum(["pending", "paid"]),
  date: z.string(),
});

const CreateInvoice = FormSchema.omit({ id: true, date: true });
```

z.coerce.number 这个就是将 amount 这个字段先强转为 number 类型, 然后再判断是不是 number, 因为 input type=number 的时候, 其实实际上传给 form 的类型还是字符串类型

omit 是删除这两个字段的意思, true 只是删除确认, 没有特别含义

这个 CreateInvoice 就是从 FormSchema 派生出来的新模板约束而已

```tsx
export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get("customerId"),
    amount: formData.get("amount"),
    status: formData.get("status"),
  });
}
```

定义之后就像这样去解析

# revalidatePath

```tsx
import { revalidatePath } from "next/cache";
```

```tsx
revalidatePath("/dashboard/invoices");
```

作用就是消除对应路由页面的缓存, 不然就会一直显示旧的数据

# redirect

```tsx
import { redirect } from "next/navigation";
```

```tsx
redirect("/dashboard/invoices");
```

# 带参数的路由

比如/user/:id 这种路由,id 这个段文件夹的名字就应该叫[id]

# 创建 next 项目的命令

```bash
pnpm dlx create-next-app@latest my-app
```

# app router

就是 layout page loading 这一套东西

# react compiler

创建项目的时候,每次都弹出来问我要不要选 react compiler,听说是不需要我手动去做优化,建议还是开吧
