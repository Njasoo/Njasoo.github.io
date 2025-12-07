---
title: 学习day23
date: 2025-12-06 10:08:47
tags: ["react", "next", "shadcn/ui"]
---

# 安装 shadcn/ui

```bash
pnpm dlx shadcn@latest init
```

shadcn 不是传统的组件库,安装方法有点不太一样

# 不要在根目录的 layout 里面写 hook

layout 变成客户端的话,整个应用就会变成客户端,把需要用到 hook 的组件单独拆分

# 使用 shadcn 组件

```bash
pnpm dlx shadcn@latest add switch
```

每一次用一个新的组件,都要跑类似的命令

# useEffect 基本上是用来替代 vue 的 watch 的功能

react 没有单独对某个变量进行监视的函数

# 获取 html 标签设置 dark mode

```tsx
useEffect(() => {
  const root = document.documentElement;
  if (isDark) {
    root.classList.add("dark");
    setThemeText("深色模式");
  } else {
    root.classList.remove("dark");
    setThemeText("浅色模式");
  }
}, [isDark]); //当isDark发生改变的时候才会重新执行
```

在 html 的标签上的 class 加上 dark 就能变成深色模式的这个逻辑是 tailwindcss 提供的,不是 shadcn/ui

# next 的 env 文件配置

和 vue 一样都是分为.env.development 和.env.production

变量的前缀是 NEXT_PUBLIC

# fetch 的数据不是放在 data 里面的

```tsx
useEffect(() => {
  console.log(`${process.env.NEXT_PUBLIC_API_BASE}/source/`);
  fetch(`${process.env.NEXT_PUBLIC_API_BASE}/source/`)
    .then((res: any) => res.json())
    .then((data) => {
      platformList = data;
      console.log(data);
    })
    .catch((err: any) => console.error(err));
}, []);
```

res=>res.json()是把 response.body 里面的数据拿出来进行格式化

拿数据的流程大概是这样,记得传一个空的依赖列表,不然会无限渲染,传了的话只会在第一次渲染执行里面的逻辑

# 一个服务端 fetch 的例子

```tsx
async function PlatformSelector() {
  const res = await fetch(`${process.env.NEXT_PUBLIC_API_BASE}/source/`);
  const platformList: Source[] = await res.json();

  return (
    <Select>
      <SelectTrigger className="w-[180px]">
        <SelectValue placeholder="选择平台" />
      </SelectTrigger>
      <SelectContent>
        <SelectGroup>
          <SelectLabel>平台</SelectLabel>
          {platformList.map((platform) => (
            <SelectItem key={platform.id} value={platform.value}>
              {platform.name}
            </SelectItem>
          ))}
        </SelectGroup>
      </SelectContent>
    </Select>
  );
}
```

直接用 await 就好了,这样就不用 useEffect,也不用将 platformList 定义成 state,直接定义成普通变量,然后 await 结果就行

# store 的类型定义,setter 的类型也要写上

```ts
export type PlatformStore = {
  currentPlatform_en: string;
  currentPlatform_zh: string;
  currentCategories: Category[];
  setCurrentPlatform_en: (value: string) => void;
  setCurrentPlatform_zh: (value: string) => void;
  toggleCategory: (categoryName: string) => void;
};
```

# 加上 persist 之后如何指定泛型

```ts
import { create } from "zustand";
import { persist } from "zustand/middleware";
import { Category, PlatformStore } from "../definition";

export const usePlatformStore = create(
  persist<PlatformStore>(
    (set) => ({
      currentPlatform_en: "weibo",
      currentPlatform_zh: "微博",
      currentCategories: [
        { category: "文化", text: "文化", checked: true },
        { category: "国际新闻", text: "国际新闻", checked: true },
        { category: "财经新闻", text: "财经新闻", checked: true },
        { category: "体育", text: "体育", checked: true },
        { category: "娱乐", text: "娱乐", checked: true },
        { category: "港澳政治", text: "港澳", checked: true },
        { category: "中国大陆政治", text: "中国大陆", checked: true },
      ],

      setCurrentPlatform_en: (newPlatform: string) =>
        set({ currentPlatform_en: newPlatform }),

      setCurrentPlatform_zh: (newPlatform: string) =>
        set({ currentPlatform_zh: newPlatform }),

      toggleCategory: (categoryName: string) =>
        set((state: PlatformStore) => ({
          currentCategories: state.currentCategories.map((c: Category) =>
            c.text === categoryName ? { ...c, checked: !c.checked } : c
          ),
        })),
    }),
    {
      name: "platform-storage",
    }
  )
);
```

# 普通变量依赖 state

听说一个普通变量如果是通过 state 来生成的话,那么 state 改变的时候会引起组件的重新渲染,那么这个普通变量,某种程度上也可以看作是一个 state,当然它不是,但是效果很相似

# 从 store 拿属性

```tsx
const pageNumber = usePageNumberStore((state) => state.pageNumber);
```

如果直接这样的话,会变成 undefined

```tsx
const store = usePageNumberStore();
const pageNumber = store.pageNumber;
```

# 所有的 hook 必须放在函数的最顶层

比如 store 的属性,useState 之类的

# 凡是能由 state 推算出来的变量都不要再定义成 state

因为 state 之间的更新是异步的,自己维护可能拿旧的值去做了计算

# 一个传多个属性的 props 的简便方法

```tsx
<div className="w-[95%] mx-auto">
  {newsList.map((news) => (
    <NewsItem {...news} />
  ))}
</div>
```

这样就不用一个一个字段去写
