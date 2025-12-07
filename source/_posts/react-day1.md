---
title: react day1
date: 2025-11-04 13:10:30
tags: ["react"]
---

# 创建 react 项目

vite 不仅可以创建 vue 项目，还可以用来创建 react 项目

```bash
npm create vite@latest my-react-app -- --template react
```

然后就是一样的运行命令

```bash
cd my-react-app
npm install
npm run dev
```

# react 组件

组件是一个 jsx 后缀的文件，jsx 就可以理解为加强版的 js 吧，这个文件里面要写一个函数，然后返回一个类似 html 文件格式的数据

还有一件事要说的就是，react 组件的首字母一定要大写，不然会被识别成 html 的组件，而不是 react 组件

```jsx
import { useState } from "react";

function App() {
  const [text, setText] = useState("");

  return (
    <div style={{ padding: 20 }}>
      <input
        value={text}
        onChange={(e) => setText(e.target.value)}
        placeholder="在这里输入"
      />
      <p>你输入的是：{text}</p>
    </div>
  );
}

export default App;
```

return 的 html 树一定只能有一个节点，可以直接用<></>包住也行

# useState

```jsx
import { useState } from "react";
const [val, setVal] = useState(0);
```

这是创建一个响应式变量的约定俗成的方式，useState 里面的参数是初值，val 是响应式变量，然后 setVal 是修改对应状态的函数

# 插入 js 表达式

在 react 的组件里面使用 js 变量的方法是用单大括号{}包住，并没有双重括号{{}}的做法

# onChange

因为 vue 用太多了，原生代码写得太少，我都没怎么用过这个，onChange 的作用就是比如输入框的内容改变的时候，就会触发的一个事件

# 自动格式化代码一个比较好用的插件-Prettier

好像支持的语言特别广，什么都能自动格式化，比如我现在在写的 md。原来这个玩意只支持前端方面的语言，python 语法它无法自动格式化

# onClick 和 onclick 是不一样的

onClick 或者 onChange 这些是 react 专用的语法，这些函数要配套 react 的 state 使用，如果直接用 onclick 这些是不能做到更新变量的

# props

父组件向子组件传数据

```jsx
function App() {
  return (
    <div>
      <Greeting name="小李" age={21} />
      <Greeting name="小王" age={20} />
    </div>
  );
}
```

```jsx
export default function Greeting({ name, age }) {
  return (
    <div>
      <p>你好，{name}</p>
      <p>年龄：{age}</p>
    </div>
  );
}
```

子组件那边在函数那边定义参数来接受 props，本质上父组件传过来的参数就是一个对象，要用一个{}包住，这已经是解构的写法了，其实直接写成 props 也行，我感觉直接写 props 的写法可能比较适合会不断更新的地方吧

注意 react 的 props 和 vue 一样，是不能修改的数据

# 解释一下 main.jsx

和 vue 的 main.js 功能极度相似，用来引入各种全局配置

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import App from "./App.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

StrictMode 是一个开发阶段的工具组件，反正就是用来提供一些 debug 的作用

index.css 通常是一个全局的 css 配置，其他 css 文件就是通常放在 src/styles 这个目录里面（自己定义），assets 这个文件夹是放静态媒体资源的

# 列表渲染

react 里面用数组自带的 map 方法来进行列表的渲染，这里举个例子，每个列表项一定要指定一个 key，这个 key 的作用好像是用来让 DOM 找到一个对应的元素，来做到最小更新，没有 key 的话可能也会出现一些渲染错误，在动态更新的页面当中更容易出错，建议是直接用 id 来做 key，这个 id 的来源可能是数据库里面的 id，用数组下标是不太推荐的，列表动态更新的时候 UI 会出错，为什么呢，比如你删除一个元素的时候，某些元素的下标就改变了，所以这个 key 我们应该选择一些静态的唯一值

顺便说一下 item=>后面跟的不是{}而是()，这个 js 语法不是很纯正，这是混合的 jsx 语法，蛮酷的

```jsx
function TodoList() {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          {todo.text} {todo.done ? "✅" : "❌"}
        </li>
      ))}
    </ul>
  );
}
```

# useState 的 set 函数之旧值换新值

```jsx
<button onClick={() => setShowOnlyUndone((s) => !s)}></button>
```

这个 s 参数就是旧值，其实这样写不是为了方便，如果直接写 setShowOnlyUndone(!showOnlyUndone)，其实是有问题的，就是 jsx 代码是异步的，那么这个 showOnlyUndone 的值可能每次更新都不是基于最新的值，用上面的写法能保证每次更新都是基于最新的值

# React 的更新机制

React 并没有所谓的响应式变量，有一些用 useState 定义的变量发生改变的时候，整个 react 组件都会重新更新，那么有些数组（只是普通的 js 数组）如果是根据这些 useState 变量来计算的话，那么也会重新更新，这里给一段具体的代码

```jsx
import { useState } from "react";

const initial = [
  { id: 1, text: "学 React", done: false },
  { id: 2, text: "做项目", done: true },
  { id: 3, text: "写简历", done: false },
];

function TodoFilter() {
  const [showOnlyUndone, setShowOnlyUndone] = useState(false);
  const list = showOnlyUndone ? initial.filter((item) => !item.done) : initial;

  return (
    <div>
      <button onClick={() => setShowOnlyUndone((s) => !s)}>
        {showOnlyUndone ? "显示全部" : "只显示未完成"}
      </button>
      <ul>
        {list.map((item) => (
          <li key={item.id}>
            {item.text} {item.done ? "✅" : "❌"}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodoFilter;
```

这个 list 变量会在 showOnlyUndone 变量发生改变的时候，也重新计算，所以页面并不会保持不变，然后这个组件的更新范围是 jsx 文件的组件函数，而不是从 react 的根节点开始更新，子变父不一定变，但父变子一定变，因为父引用了子，父重新执行，必然重新引用子

# 前端测试网站

提供假数据给前端 fetch

https://jsonplaceholder.typicode.com

https://example.com

# useEffect

有点复杂这玩意，先从一个简单的没有依赖数组的样例来解释

```jsx
import { useState, useEffect } from "react";

function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    let cancelled = false;
    fetch("https://jsonplaceholder.typicode.com/users")
      .then((r) => r.json())
      .then((data) => {
        if (!cancelled) setUsers(data);
      });
    return () => {
      cancelled = true;
    };
  }, []);

  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}

export default Users;
```

在这里依赖数据失控的，那么这里 useEffect 里面的代码就是在组件挂载的时候会执行一次，之后不会再执行了，然后里面的 return ()=>{cancelled=true}就是清理函数，这是在组件卸载的时候才会执行的，这里的作用是为了避免组件在还没有获得数据的时候就 setUsers

然后我查了一下 ai，说是有依赖数组的话，比如是[a,b]，a 和 b 在渲染后发生改变的时候，就会重新执行里面的代码，如果依赖数组这个位置的参数直接空着（不是[]的意思，而是直接不传），就是每次组件渲染的时候，都会重新执行一次代码

什么是渲染，就是 react 重新调用组件函数，重新计算要显示的内容这件事情叫做渲染

依赖数组的变量类型没有严格要求，但是通常要求是 state 或者 props 的数据，总的来说就是要放会一些值改变之后会导致重新渲染的变量在依赖列表当中，比如说 a 是一个 state，然后 b=a\*2，b 是一个普通变量，但是它依赖于 a，b 改变，这意味着 a 发生了改变，这是一个传递性，所以 b 的改变也意味着重新渲染

# 路由

在 react 当中，我们用的是 react-router-dom，简单做个安装

```bash
npm install react-router-dom
```

在定义路由的时候，可以选择直接写在 App.jsx 里面，或者单开一个/src/router/index.jsx，这个方式是比较工程化的，也是我之前在写 vue 项目时用的方法，我先放一个写在 App.jsx 的做法

```jsx
import Home from "./pages/Home";
import About from "./pages/About";
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <nav style={{ display: "flex", gap: "1rem" }}>
        <Link to="/">首页</Link>
        <Link to="/about">关于</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

Routes 里面包着 Route，然后 Routes 一定要被包在 BrowserRouter 里面，这个 BrowserRouter 的作用就好像是 vue-router 里面的 history 一样，所以还有一个 HashRouter，作用就是用来提供路由上下文，其实不是很多是什么，然后放里面就对了，然后这个 BrowserRouter 还有一个类似 div 的作用，所以也可以在里面写一些常规的组件

单独把路由分离出来的写法就是下面这样

```jsx
import { Route, Routes } from "react-router-dom";
import Home from "../pages/Home";
import About from "../pages/About";

function AppRouter() {
  return (
    <Routes>
      <Route path="/" element={<Home></Home>}></Route>
      <Route path="/about" element={<About></About>}></Route>
    </Routes>
  );
}

export default AppRouter;
```

有个类似于 router.push 的功能就是 useNavigate

首先引入函数

```jsx
import { useNavigate } from "react-route-dom";
```

```jsx
const navigate = useNavigate();
```

```jsx
navigate("/"); // 具体的跳转
```

# 动态路由

动态路由就是类似于/login/:id 这种形式的，因为这个 id 不是固定的，所以动态发生改变的

我直接从 ai 那边偷了一个例子

```jsx
import { useParams } from "react-router-dom";

export default function User() {
  const { id } = useParams();
  return <h1>用户ID是：{id}</h1>;
}
```

路由那边的配置是这样的，用 useParams()就能捕获到 url 里面的参数

```jsx
<Route path="/user/:id" element={<User />} />
```

# Outlet

```jsx
import { Outlet, Link } from "react-router-dom";

export default function MainLayout() {
  return (
    <div>
      <nav
        style={{
          display: "flex",
          gap: "1rem",
          padding: "1rem",
          borderBottom: "1px solid #ccc",
        }}
      >
        <Link to="/">首页</Link>
        <Link to="/about">关于</Link>
        <Link to="/user/1">用户1</Link>
      </nav>
      <div style={{ padding: "1rem" }}>
        <Outlet></Outlet>
      </div>
    </div>
  );
}
```

Outlet 就是一个占位符，用途就是用来展示匹配到的子路由的页面

# 子路由

我直接给个代码先

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import MainLayout from "./layouts/MainLayout";
import Home from "./pages/Home";
import About from "./pages/About";
import User from "./pages/User";

export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<MainLayout />}>
          <Route index element={<Home />}></Route>
          <Route path="about" element={<About />}></Route>
          <Route path="user/:id" element={<User />}></Route>
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

被 Route 包住的 Route 就是子路由，具体作用就和 vue-router 的 children 作用一样，那个带有 index 的 route 就是，默认的子路由，就是它的 url 和父路由的 url 是一样的，直接写 path=""也是可以，顺便说一下，子路由的 path 是直接附加在副路由的 url 前缀后面的，所以不需要带上"/"这个符号

# 状态管理工具-Zustand

听说和 pinia 很相似，然后最早的状态管理工具应该是 redux，很多大企业在用，但是听说很难用，现在都逐渐在换成 zustand 了，所以我还是选择学 zustand 了

也是简单地走个安装流程

```jsx
npm install zustand
```

定义 store 文件用 js 文件就行，不用 jsx
