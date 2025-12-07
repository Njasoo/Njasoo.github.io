---
title: 学习day10
date: 2025-11-20 13:57:41
tags: ["文本分类"]
---

# 清华源下载

```bash
pip install transformers -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install torch -i https://pypi.tuna.tsinghua.edu.cn/simple

```

# django ORM 批量更新对象

```py
def run():
    queryset = HotItem.objects.all()
    hoitems_to_update = []
    for hotitem in queryset:
        result = classifier(hotitem.title)[0]
        result = en2zh[result["label"]]
        hotitem.category = result
        hoitems_to_update.append(hotitem)
    HotItem.objects.bulk_update(
        hoitems_to_update, ["category"]
    )  # 第二个参数也是指定要更新的列，这样就不会更新整个对象
```

逐个 save 是很耗时间的

# watch immediate 参数

```ts
watch(
  () => router.currentRoute.value.path,
  (new_path) => {
    const matched = page2category.find((item) => item.path == new_path);
    if (matched) {
      current_category = matched.category;
    }
  },
  { immediate: true }
);
```

watch 只在监听的变量变化的时候触发，immediate 可以让里面的逻辑在变量初始化的时候也触发一次

# 路由变换的时候强制刷新

有时候，不同 url 指向了同一个组件，导致切换路由的时候没有更新页面，解决方法如下

```html
<router-view :key="$route.fullPath"></router-view>
```

$router.fullPath 是 vue-router 提供的属性，这段代码的含义是当路由变化的时候会强制刷新组件
