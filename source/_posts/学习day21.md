---
title: 学习day21
date: 2025-12-04 10:52:34
tags: ["react", "next"]
---

# layout

layout 是公共的模板

```tsx
import SideNav from "@/app/ui/dashboard/sidenav";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

children 是 react 自动注入的, 通常就是目录或者子目录下对应的 page.tsx, 或者是子目录里面的 layout

有点像 vue 的 children 里面的父组件的部分

用 layout 有什么好处, 就是被嵌套在 layout 里面的子组件重新渲染的时候, 不会引起 layout 的重渲染, 这叫什么 partial rendering, 是性能优化的一部分

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

根目录一定要这么写

# Link 组件

```tsx
import Link from "next/link";
```

普通的 a 标签进行导航的时候都会导致页面的重新刷新, 这样太耗费时间, 用 Link 组件进行导航, 可以在客户端进行页面的切换, 不需要刷新

```tsx
<Link
  key={link.name}
  href={link.href}
  className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
>
  <LinkIcon className="w-6" />
  <p className="hidden md:block">{link.name}</p>
</Link>
```

Link 的使用方法

# Link 预加载

预加载发生在, Link 组件在用户的可视范围内, 然后浏览器就会预加载 Link 指向的页面, 而这件事情只在生产环境有效

# Hook 只能在客户端使用

```tsx
"use client";

import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from "@heroicons/react/24/outline";
import Link from "next/link";
import { usePathname } from "next/navigation";

// ...
```

这里引入了一个 usePathname, 这是一个 hook, 所以要在文件的顶部写上'use client', 没有写这句的默认都是服务器端组件, client 端组件的子组件都是 client

# 服务器端和客户端的区别

服务器的数据好像会先渲染, 如果没有服务器端, 正常的页面生成流程是这样, 发送请求, 下载所有 js, 执行 js, 请求数据, 渲染页面

但是服务器端请求页面时会直接生成 html, 怎么说了, 其实不是很懂, 但是没有服务器端的话, 搜索引擎是搜不到网站的内容的, 因为所有东西都放在了客户端, 那么搜索引擎搜的时候就是空壳

一个页面能放到服务器端的话, 就放到服务器端, 性能更好, 也更容易宣传

# clsx 使用方法

```tsx
import clsx from "clsx";
```

```tsx
<Link
  key={link.name}
  href={link.href}
  className={clsx(
    "flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3",
    {
      "bg-sky-100 text-blue-600": pathname === link.href,
    }
  )}
>
  <LinkIcon className="w-6" />
  <p className="hidden md:block">{link.name}</p>
</Link>
```

# vercel 也可以创建 database

# 在 next 当中引用 sql

```ts
import postgres from "postgres";

const sql = postgres(process.env.POSTGRES_URL!, { ssl: "require" });

// ...
```

ssl 是加密的意思

# 标签模板字面量

## 模板字面量

```js
const name = "Alice";
const age = 20;

const str = `My name is ${name} and I am ${age} years old.`;
console.log(str); // My name is Alice and I am 20 years old.
```

这是一个普通的模板字面量

## 真正的标签模板字面量

```js
function tag(strings, ...values) {
  console.log(strings);
  console.log(values);
}

const name = "Alice";
const age = 20;

tag`My name is ${name} and I am ${age} years old.`;
```

运行结果如下

```js
strings: ["My name is ", " and I am ", " years old."];
values: ["Alice", 20];
```

简单地来说就是把静态的字符串和参数部分分离了

标签模板函数会把参数分成两个部分, 一个是静态字符串数组, 另外一个就是占位参数的数组, 如果函数只写了一个参数的话, 就是只拿静态的部分

所以正常的定义方法都是按照上面的方法定义

# Promise.all 的用法

```ts
const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
const invoiceStatusPromise = sql`SELECT
         SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
         SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
         FROM invoices`;

const data = await Promise.all([
  invoiceCountPromise,
  customerCountPromise,
  invoiceStatusPromise,
]);
```
Promise.all里面的请求都是异步的, 但是它们是并行的

# loading.tsx
当前路由的page.tsx还没有完全渲染成功的时候, 会自动切换到loading.tsx里面显示里面的内容

# vscode设置缩进
```json
"editor.tabSize": 2,
"[python]": {
    "editor.tabSize": 4
},
```
在settings.json里面设置

# (overview)文件夹
创建一个名为(overview)的文件夹, 不会改变路由的结构, /dashboard/(overview) = /dashboard, 这个文件夹的作用主要是因为有些时候loading会影响所有子目录的page, 但是我不想产生这样的效果, 所以我选择要让目录的loading和子目录里面的文件同级的话, 选择再加一层(overview)文件夹

其实不一定要叫(overview), (xxx)都行, 加括号就行

# Suspense
给一个组件包独立的loading
```tsx
<Suspense fallback={<RevenueChartSkeleton />}>
  <RevenueChart />
</Suspense>
```
这个RevenueChart, 它的加载时间明显比其他组件要长, 所以我们给它加上单独的Suspense组件来把它包起来, 其他组件加载出来的时候, 这个组件就会单独继续加载显示skeleton, 当没有加载完成的时候就显示fallback里面的组件

这里RevenueChart有一个revenue, 本来是要父组件请求完之后然后通过prop传给它的, 但是这里因为请求的时间太久了, 导致请求的结果被卡住了, 所以请求的动作放到了, RevenueChart里面进行单独的请求

# searchParams
```tsx
import { useSearchParams } from 'next/navigation';
```
```tsx
const searchParams = useSearchParams();
```
这段代码返回一个只读的对象, 包含了URL的参数, 比如?page=2&id=114514就返回
```json
{
  "page": 2,
  "id": 114514,
}
```

# URLSearchParams
一个用来设置URL查询参数的类
```js
const params = new URLSearchParams();
params.set("page", 1);
params.set("id", 114514);
console.log(params); // URLSearchParams { 'page' => '1', 'id' => '114514' }
console.log(params.toString()); // page=1&id=114514
```

# usePathname
```tsx
import { usePathname } from "next/navigation";
```
```tsx
const pathname = usePathname();
```
pathname就是当前路由的字符串, 本质上这个usePathname就是一个hook

# defaultValue
组件的初始值, 只在第一次渲染的时候生成
```tsx
<input defaultValue={state} />
```
像这样写, 能绑定上值, 但是后续state改变值之后, 也不会影响input的值

# 解释一个参数
```tsx
export default async function Page(props: {
  searchParams?: Promise<{
    query?: string;
    page?: string;
  }>;
})
```
这里props是一个对象, 里面可能包含一个searchParams属性, 如果包含的话, 这个searchParams是一个Promise对象, 里面的字段表示这个Promise resolve之后包含一个对象, 然后里面也可能包含query和page这两个属性

# use-debounce
```bash
pnpm i use-debounce
```
```tsx
import { useDebouncedCallback } from 'use-debounce';
```
用法和之前的lodash调用的useDebounceFn是一样的