---
title: 学习day13
date: 2025-11-26 16:17:28
tags: ["vue"]
---

# pinia 持久化

```bash
pnpm install pinia-plugin-persistedstate
```

```ts
import { defineStore } from "pinia";

export const useNewsStore = defineStore("news", {
  state: () => ({
    current_platform: "bilibili",
  }),
  persist: true,
});
```

state 是一个 reactive 对象

v-model 可以绑定 reactive 对象的属性，watch 属性要写 getter
