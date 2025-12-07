---
title: 学习day17
date: 2025-11-30 21:54:19
tags: ["vue"]
---

# ref 数组 key 选定问题

```html
<button
  class="join-item btn bg-base-100 shadow-sm"
  v-for="(item, index) in page_list"
  :key="item + '-' + index"
>
  {{ item }}
</button>
```

有重复的元素，以及没有固定的 id，可以用元素加下标的方式渲染

# :class 的 bg 不能覆盖之前的 bg

```html
<button
  class="join-item btn shadow-sm"
  v-for="item in page_list"
  :key="item"
  :class="currentPageNumber == item ? 'bg-accent' : 'bg-base-100'"
  @click="pageJumpHandle(item)"
>
  {{ item }}
</button>
```

# 原生事件触发函数和 vue 的区别

```html
<input type="text" id="input_box" onchange="input_change_handle()" />
```

没有参数也可以写括号，vue 那边写了括号会导致函数在事件发生之间就被调用了

# event 对象

```html
<input type="text" id="input_box" onchange="input_change_handle(event)" />
```

有参数的话，html 这边一定要这么写，但是 script 那边可以随便写，因为这个 event 要传入的话，一定要存在，这个 event 是 dom 提供的全局对象

# onchange 和 oninput

onchange 是输入框失去焦点的时候触发，oninput 是实时和输入的内容同步
