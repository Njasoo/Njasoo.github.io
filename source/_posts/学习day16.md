---
title: 学习day16
date: 2025-11-29 12:17:35
tags: ["vue"]
---

# 可选链不能用在 v-model 上

```html
<input type="checkbox" class="checkbox checkbox-sm" v-model="item?.checked" />
```

# django 或查询

```py
def get_queryset(self):
    queryset = HotItem.objects.all()
    source = self.request.GET.get("source", None)
    categories = self.request.query_params.getlist(
        "categories", None
    )  # 列表要用getlist
    if source:
        queryset = queryset.filter(source=source)  # 不赋值是不会修改queryset的
    queryset = queryset.filter(
        category__in=categories
    )  # 相当于category在categories当中出现的行就会返回
    queryset = queryset.order_by("rank")
    return queryset
```

# ref 对象数组，watch 属性没有用

```ts
watch(
  categories,
  (newVal) => {
    console.log("变化了", newVal);
  },
  { deep: true }
);
```

这样才有用

# 接口参数传数组的问题

直接传数组,会变成 categories=a,b，Django 那边就是["a,b"]，但是我实际想要["a","b"]，下面是解决方法

后端 django getlist 方法就是把对应的 key 的 value 集合在一起

```ts
import request from "./request";

export const getHotItems = (source: string, categories?: string[]) => {
  const params = new URLSearchParams();
  params.append("source", source);
  categories?.forEach((c) => {
    params.append("categories", c);
  });
  // 生成的 URL 例子: hot/?source=哔哩哔哩&categories=文化&categories=财经
  return request.get(`hot/?${params.toString()}`);
};
```

# 禁止 flex 子元素收缩

```html
<div
  class="flex mx-auto space-x-5 bg-base-100 p-4 mt-3 shadow-sm w-[95%] overflow-x-auto flex-nowrap items-center"
>
  <div
    v-for="item in newsStore.current_categories"
    :key="item.text"
    class="flex flex-row items-center shrink-0 space-x-1"
  >
    <span class="text-sm mx-1">{{ item?.text }}</span>
    <input
      type="checkbox"
      class="checkbox checkbox-sm"
      v-model="item.checked"
    />
  </div>
</div>
```

shrink-0 配合 overflow-x-auto 使用，就可以实现屏幕不够大时可以滑动
