---
title: react-day2
date: 2025-11-06 12:58:11
tags: ["react", "react-router-dom"]
---

# Zustand

继续来学习这个状态管理工具，首先讲解一下这个定义 store 的部分

```js
import { create } from "zustand";

export const useTodoStore = create((set) => ({
  todos: [],

  setTodos: (todos) => set({ todos }),
  addTodo: (text) =>
    set((state) => ({
      todos: [...state.todos, { id: Date.now(), text, done: false }],
    })),
  toggleTodo: (id) =>
    set((state) => ({
      todos: state.todos.map((t) =>
        t.id === id ? { ...t, done: !t.done } : t
      ),
    })),
  removeTodo: (id) =>
    set((state) => ({
      todos: state.todos.filter((t) => t.id !== id),
    })),
}));
```

解释一下这段代码，首先 todos 就是 state 的属性之一，然后下面那些函数都是一些 action，用来修改数据的，因为 zustand 不允许直接修改 state 的数据，一定要通过 set 这个函数来进行修改，而 set 这个函数是 zustand 提供的，set 一定要返回一个对象，所以你都会看到函数体都是用括号包住的，state 是指当前的 store 属性全体，要访问 todos 这个属性的话，就写 state.todos

讲一下其中一个比较难懂的地方

```js
t.id === id ? { ...t, done: !t.done } : t;
```

这里的...t 的意思就是先把复制过来，然后单独再修改 done 这个字段，可以理解为覆盖吧，第一次看有点难以理解这个语法

# 数据持久化

zustand 也可以做到和 vue 的 pinia 一样的数据持久化，就是刷新网页之后 state 的数据不会刷新，这里的原理就是把数据存到 localstorage 当中，下面给个代码

```js
import { create } from "zustand";
import { persist } from "zustand/middleware";

export const useTodoStore = create(
  persist(
    (set) => ({
      todos: [],
      setTodos: (todos) => set({ todos }),

      addTodo: (text) =>
        set((state) => ({
          todos: [...state.todos, { id: Date.now(), text, done: false }],
        })),
      toggleTodo: (id) =>
        set((state) => ({
          todos: state.todos.map((t) =>
            t.id === id ? { ...t, done: !t.done } : t
          ),
        })),
      removeTodo: (id) =>
        set((state) => ({
          todos: state.todos.filter((t) => t.id !== id),
        })),
    }),
    {
      name: "todo-storage",
    }
  )
);
```

这个 name 就是 localstorage 的 key

# 组件库-Antd

Antd 是国内比较常用的组件库，如果说是世界范围的话，MUI 是更加常用的，就是 google 的那一套组件库，其实我看了一下也感觉蛮好看的，但是为了准备字节的面试，所以我还是选择用 Antd 了，首先先来做个安装

```bash
npm install antd
```

问了一下 ai，他说 antd 从 v5 开始默认就是按需引入的了，不需要像 element-ui 那样安装一个什么 auto-import 的插件了

# antd 组件-Space

这个组件的作用就是给组件里面的元素自动添加间距，这玩意好像本身就是一个 flex 容器，所以可以用一些相关的属性

# button 的 danger 属性

这个属性就是提醒用户，这个按键按下时候可能会造成一些不可逆的操作，要谨慎之类的

# 非常神奇的一个报错

```jsx
<Button danger type="link" onClick={() => deleteTodo(todo.id)}>
  删除
</Button>
```

这样是对的

```jsx
<Button danger type="link" onClick={deleteTodo(todo.id)}>
  删除
</Button>
```

这样是错的

问了 ai，它的解释非常奇怪，但是某种程度上也算是可以理解，它说 deleteTodo(todo.id)这种写法就是已经把函数给执行了，所以当 Todo 被创建的时候，同时又被删除了，等于没有创建，而把() => deleteTodo(todo.id)传进去相当于传一个函数的引用，但是还没有执行，哎，总之以后就写一个匿名函数传进去就是了

# antd 组件-List

一个列表渲染的组件，我先放一段代码，然后基于代码解释

```jsx
<List
  dataSource={todos}
  renderItem={(todo) => (
    <List.Item>
      <TodoItem todo={todo} />
    </List.Item>
  )}
/>
```

首先这个 dataSource，很显然就是数据源，然后 renderItem，很简单，就是可以自定义每个列表项的渲染方式，然后再用 jsx 语法去写就是了，不过感觉还是有点怪的，这个渲染方式居然是写在属性那边的
