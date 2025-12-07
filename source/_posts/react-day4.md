---
title: react-day4
date: 2025-11-08 14:24:14
tags: ["react"]
---

# Arco 组件库

一开始以为是哪里来的野鸡 UI 库，但是听说这是字节开发的，那不得不学了，安装方法如下

```bash
npm install @arco-design/web-react
```

# 复习一下 axios 吧

在前端的 src 文件夹底下创建一个 api 文件夹，然后在里面再创建一个 request.js，具体的代码如下

```js
import axios from "axios";

const request = axios.create({
  baseURL: "https://127.0.0.1:8000/api/",
  timeout: 5000,
});

export default request;
```

这里的 timeout 单位是 ms

然后我们在同一个 api 目录底下编写关于模型的增删改查封装接口，首先是 todos，创建一个 todo.js，然后加上这些代码

```js
import request from "./request";

export const getTodos = () => request.get("todos/"); //记得不要加上大括号，加上的话是没有返回值的
export const createTodo = (data) => request.post("todos/", data);
export const updateTodo = (id, data) => request.put(`todos/${id}`, data);
export const deleteTodo = (id) => request.delete(`todos/${id}`);
```

顺便说一下，axios 的增删改查函数返回的结果都是 promise 对象，也就是说可以用 then 来写回调函数

# cors 配置再复习

妈的，忘记在 settings 的 installed_apps 加上关于 cors 的 app 了，具体加法是这样的

```py
INSTALLED_APPS = [
    ...
    "corsheaders",
]

MIDDLEWARE = [
    ...
    "corsheaders.middleware.CorsMiddleware",  # 放前面！！
    "django.middleware.common.CommonMiddleware",
]

CORS_ALLOW_ALL_ORIGINS = True  # 允许所有的请求源

# CORS_ALLOWED_ORIGINS = [
#     "http://localhost:5173",
#     "http://127.0.0.1:5173",  # 这个是比较规范的写法
# ]
```

加上这些代码就行了，反正每安装一个库，就看一下 installed_apps 这里是不是要加上，没加上就完全用不了

# create 接口的优化

```js
const handleAdd = () => {
  if (!title.trim()) return;
  createTodo({ title, completed: false })
    // .then(() => {
    //   //其实create是有返回数据的，但是我可以选择忽略
    //   setTitle("");
    //   fetchTodos();
    // }) //但是感觉用这个刚创建的对象直接更新数组好像更快啊，少了一次请求
    .then((res) => {
      setTodos((s) => [...s, res.data]);
      setTitle("");
    })
    .catch((err) => console.error(err));
};
```

原因已经写在注释那里了

# 前端 put 请求优化

```js
const handleToggle = (todo) => {
  updateTodo(todo.id, {
    title: todo.title,
    completed: !todo.completed,
  })
    .then(() => {
      setTodos((s) =>
        s.map((t) => (t.id === todo.id ? { ...t, completed: !t.completed } : t))
      );
    })
    .catch((err) => console.error(error));
};
```

直接 getTodos 了也行，但是多一次请求，这样算是一种优化方案吧，就是尽量地减少请求的次数，或许面试的时候可以说一说

# url 记得加上斜杠

drf 是严格遵循标准的，末尾没有加上斜杠的话，是不会自动补上，会直接报错，另外想说一下 put 感觉不是很好用，要传入整个对象，好像还是 patch 比较好用

# 异步的问题又出现了

```jsx
onClick={() => {
                deleteTodo(todo.id)
                  .then(fetchTodos())
                  .catch((err) => console.error(err)); //也是懒到一个极致
              }}
```

这样写是不行的，因为 fetchTodos()就直接执行了，

```jsx
onClick={() => {
                deleteTodo(todo.id)
                  .then(() => fetchTodos())
                  .catch((err) => console.error(err)); //也是懒到一个极致
              }}
```

要么就这样写

```jsx
onClick={() => {
                deleteTodo(todo.id)
                  .then(fetchTodos)
                  .catch((err) => console.error(err)); //也是懒到一个极致
              }}
```

要么直接传函数引用进去，但是这样有点难理解，建议还是用上面那种比较常见的方式来做

# 一个冷知识

其实 await 和 then 的作用是一样的，可能 await 的代码量会比较少吧，要问我比较喜欢哪一个，我会觉得 await 的代码会比较优雅

# zustand 的 get 参数

之前都是在 set 函数里面用 state 参数来访问 store 的数据，今天才发现可以直接用 get 来访问 state 的数据，具体给几个例子就应该能动了

```js
import { create } from "zustand";
import { getTodos, updateTodo, deleteTodo, createTodo } from "../api/todo";

export const todoStore = create((set, get) => ({
  //set用来设置属性，get用来访问属性
  todos: [],

  fetchTodos: async () => {
    const res = await getTodos();
    set({ todos: res.data });
  },
  addTodo: async (title) => {
    await createTodo({ title, completed: false });
    get().fetchTodos();
  },
  updateTodo: async (title) => {},
}));
```

get()直接就是 state 本体了，然后就访问里面的属性了

然后刚才也说到了为什么用 await，这个 await 和 then 回调函数是等价的，原因也很简单，就是因为这个 axios 封装的函数本身就是异步的，所以当然要写异步函数

# zustand 创建的 store 的属性都是响应式的

这里的响应式跟 vue 不一样，不过反正都可以理解为，当这些响应式的数据改变的时候，页面也会动态地改变。以后可以把一些页面要展示的内容都写在 zustand 里面然后全局引用
