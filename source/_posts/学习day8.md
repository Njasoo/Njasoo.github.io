---
title: 学习day8
date: 2025-11-18 13:28:20
tags: ["系统存储"]
---

# 建立软链接清理 C 盘

在开始今天的学习之前，我注意到了我电脑的 C 盘不知道为什么突然又快爆了，之前清理过好几次，都是一些 node 以及浏览器的缓存，不是一些很重要的东西，但是要这样频繁地去清理很麻烦，之前听说过软链接这个东西，我想用这玩意给我的 C 盘腾一点空间，毕竟我的 D 盘占用率还是比较低的

C:\Users\Njaso\AppData\Local 这个目录占了有 30GB+，但是我就想转一个 10GB 左右，底下有 Temp 目录，更新频率蛮高的，但是它的大小不大，我找了一下这里面的 Android 目录有 10GB+，就决定转移它了，尽管它上次更新已经快两年前，其他目录的文件不敢乱删

```bash
mklink /j C:\Users\Njaso\AppData\Local\Android D:\Android
```

/j 就是建立目录链接，第一个参数是目标地址，这个目标地址一定是要不存在的，第二个参数源地址

# 添加 drf 分页器

## 局部分页

```py
from .models import HotItem
from .serializers import HotItemSerializer
from rest_framework.generics import ListAPIView
from rest_framework.pagination import PageNumberPagination


class HotItemPagination(PageNumberPagination):
    page_size = 10  # 默认每页10条记录
    page_size_query_param = "page_size"  # 前端参数命名, .e.g ?page_size=114
    max_page_size = 100


class HotItemView(ListAPIView):
    serializer_class = HotItemSerializer
    pagination_class = HotItemPagination

    def get_queryset(self):
        queryset = HotItem.objects.all()
        source = self.request.GET.get("source", None)
        if source:
            queryset = queryset.filter(source=source).order_by(
                "rank"
            )  # 不赋值是不会修改queryset的
        return queryset

```

一定要用 ListAPIVIew，或者 modelViewSet，不然不会生效，max_page_size 就是允许用户重定义 page_size 的上限，超过了 max_page_size 不会发生什么，只会改回上限的值

## 全局分页

settings.py

```py
REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 10,
}
```

全局设置无法让用户传入 page_size 参数

```py
class HotItemView(ListAPIView):
    serializer_class = HotItemSerializer

    def get_queryset(self):
        queryset = HotItem.objects.all()
        source = self.request.GET.get("source", None)
        if source:
            queryset = queryset.filter(source=source).order_by(
                "rank"
            )  # 不赋值是不会修改queryset的
        return queryset
```

用了全局分页器，View 这边也不用写 pagination_class

# npm 添加自定义命令

package.json

```json
"scripts": {
    "build": "hexo generate",
    "clean": "hexo clean",
    "deploy": "hexo deploy",
    "server": "hexo server",
    "cgd": "hexo clean & hexo g & hexo d"
  },
```

执行命令的方法就是 npm run cgd

# 设置路径别名

vite.config.ts

```ts
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import tailwindcss from "@tailwindcss/vite";
import path from "path";

// https://vite.dev/config/
export default defineConfig({
  plugins: [vue(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
});
```

前端调用@的时候可能会报错，不要管

tsconfig.app.json

```json
"baseUrl": ".",
"paths": {
    "@/*": ["src/*"]
}
```

在这个文件的 compilerOptions 里面也添加上这两项

# 节流函数

手写版本

```ts
// 快速点击下一页的时候，会导致页码和实际内容不同步，这时候需要利用节流来限制短时间内频繁操作的行为
export const useThrottle = (fn: Function, delay: number = 300) => {
  let last = 0;
  return (...args: any[]) => {
    const now = Date.now();
    if (now - last >= delay) {
      last = now;
      return fn(...args);
    }
  };
};
```

节流就是为了防止短时间内多次进行同一操作，设置了每次操作的时间间隔

# composable

composable 指一些可以复用的逻辑，比如防抖节流这些（不过通常不会手写），存在/src/composables 里面

# 记录一个 bug

在组件渲染出来之前，你去找这个 HTML 元素，它可能是 null，我通过 setTimeout 延时了 500ms，发现同样的逻辑能找到这个 button
