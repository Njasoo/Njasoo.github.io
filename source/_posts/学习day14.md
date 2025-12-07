---
title: 学习day14
date: 2025-11-27 14:22:41
tags: ["scss"]
---

# scss

## 嵌套

```scss
.card {
  padding: 16px;

  .title {
    font-size: 20px;
  }

  .content {
    color: #333;
  }
}
```

## 变量

```scss
$primary: #3490dc;
$radius: 12px;

.button {
  background: $primary;
  border-radius: $radius;
}
```

## Mixin（样式片段）

```scss
@mixin center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.box {
  @include center;
}
```

## 继承

```scss
.base {
  border-radius: 10px;
  padding: 10px;
}

.card {
  @extend .base;
  background: white;
}
```

## 长样式封装

```scss
.card {
  @apply p-4 rounded-xl shadow bg-white text-gray-700;
}
```

## nextTick

```ts
getHotItems(newVal, current_category)
  .then((res: any) => {
    setUpNewsList(res);
  })
  .finally(async () => {
    await nextTick(); // ⭐ 关键：等 Skeleton 先被渲染一帧
    loading.value = false;
  })
  .catch(console.error);
```

## 类型断言

```ts
const newsList = ref<NewsItem[]>(
  new Array(10)
    .fill(null as unknown as NewsItem)
    .map(() => ({ ...tempNewsItem }))
);
```

as unknown 先隐藏，然后再断言为 NewsItem

## 小括号包大括号

```ts
new Array(10)
  .fill(null as unknown as NewsItem)
  .map(() => ({ ...tempNewsItem }));
```

这样才有 return 的含义
