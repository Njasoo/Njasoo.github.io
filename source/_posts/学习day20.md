---
title: 学习day20
date: 2025-12-03 22:43:39
tags: ["react", "next"]
---
# next/image
说是提供懒加载, 并且保证图片在加载之前保证ui不会走形, 在小屏幕上不太生成过大的照片, 之类的功能
```ts
import Image from 'next/image';
```
# 文件即路由
文件树的结构就是路由的结构, 路由的根路径在app这个文件夹, layout.tsx和page.tsx这两个文件会被自动识别, 渲染在对应的页面上面

# 组件命名没有要求
并没有硬性要求tsx/jsx的文件名要和export default的组件同名

但是一般规范最好一样, 增加易读性
