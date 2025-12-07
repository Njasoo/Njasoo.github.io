---
title: 学习day12
date: 2025-11-25 14:35:49
tags: ["vue"]
---

# vueuse/core，一个 vue 工具函数库

```bash
pnpm i vueuse/core
```

```ts
import { useThrottleFn } from "@vueuse/core";

const nextPageHandle = useThrottleFn(() => {
  if (nextPage.value == null || nextPage.value == "") {
    return;
  }
  currentPageNumber.value++;
  request
    .get(nextPage.value)
    .then((res: any) => {
      setUpNewsList(res);
    })
    .catch((err: any) => console.error(err));
}, 100);
```

节流函数示例

# 深色模式

index.html

```html
<html lang="en" data-theme="light"></html>
```

data-theme 是 daisy-ui 的主题颜色切换机制

bg-base 这类颜色都会根据主题切换

```ts
const toggleTheme = (event: Event) => {
  const checked = (event.target as HTMLInputElement).checked;
  theme.value = checked ? "dark" : "light";
  document.documentElement.setAttribute("data-theme", theme.value);
  localStorage.setItem("theme", theme.value);
};
```

text-base-content 是根据主题自动适配颜色

# 清除缓存

```py
# 清空缓存
from django.core.cache import cache

cache.clear()
print("缓存已清除")
```
