---
title: typescript问题总结
date: 2025-10-28 16:16:18
tags: ["typescript"]
---
# 变量引入
之前都是用js来写router的，现在尝试了一下用ts写，ts的问题就在于要宣告变量的类型<br>
```ts
const routes: RouteRecordRaw[] = [
    { path: "/", name: "home", component: () => import("./../views/Home.vue") }
];
```
routes的变量类型是一个包含RouteRecordRaw元素的数组，这个type需要import<br>
```ts
import { createRouter, createWebHistory, RouteRecordRaw } from "vue-router";
```
但是直接像这样引入是不行的，因为python import语句引入的都是类、函数、变量这种东西，而在这里RouteRecordRaw是一个type，要用以下语句进行引入
```ts
import type { RouteRecordRaw } from "vue-router";
```