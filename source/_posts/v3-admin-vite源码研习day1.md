---
title: v3-admin-vite源码研习day1
date: 2025-11-11 16:03:40
tags: ["vue", "v3-admin-vite"]
---

感觉不能再一直做一些简单的 xxx 管理系统了，培养不了我进阶 vue 的能力，为什么不进阶去学 react 呢，因为感觉其实选什么方向不重要，既然已经学了 vue，那么就继续往这个方向走吧，然后毕设课题也预选了一个基于 AI 的智能日记系统，想着说现在也可以精进一下自己的全栈开发技术，这样就可以在后面做课设的时候省一点力

好了现在开始来看看项目本体了，把项目克隆下来之后感觉还真是无从入手，不过项目结构还是能看得懂的，是很典型的 vue 框架的项目结构，我决定先去 App.vue 那里看一下，因为这里是整个程序最开始的地方

我看到这个 App.vue 在 script 部分引用了很多东西，好像都是主题相关的，感觉这部分暂时不是很重要，所以先看看下面的 template

```vue
<template>
  <el-config-provider :locale="zhCn">
    <router-view />
  </el-config-provider>
</template>
```

在这里看到了一个奇怪的组件，el-config-provider，还是一个 element 的组件，这个组件的功能是用来设置一些全局配置的，这里做了一个配置就是把 ui 默认的语言设置成了中文，不这样改的话，element-ui 他是默认显示英文的，这个组件还可以设置更多的全局配置，好像比如表单的大小，按键的风格，这些都可以设置，后续想要搞的话，可以再去详细查询

好了我决定跳回去看看上面的 script 部分，先来研究第一行代码

```js
const { initTheme } = useTheme();
```

useTheme 看了一下没什么好看的，来具体看一下 initTheme 写了些什么东西

```js
/** 初始化 */
function initTheme() {
  // watchEffect 来收集副作用
  watchEffect(() => {
    const value = activeThemeName.value;
    removeHtmlClass(value);
    addHtmlClass(value);
    setActiveThemeName(value);
  });
}
```

搞不懂啊，这个 watchEffect

其实和 watch 是很相似的，这个 watchEffect 首先要传一个函数进去，然后它会自动收集这段逻辑当中的每一个响应式变量，和 watch 不一样，不需要自己声明依赖的变量，而且这个 watchEffect 在创建之后会默认执行一次里面的逻辑，后续里面的响应式变量每改变一次，它都会重新执行一次里面的逻辑。说是尽量不要在 watchEffect 里面放太重的逻辑，比如 fetch 这种，但是我没有听懂，还提到了什么防抖，说是这些 fetch 的逻辑用 watch 比较好，没懂，那么下面就来研究一下什么是防抖吧

哦，原来防抖就是，当用户多次输入一些操作的时候，我们只执行最后一次操作，这样就不会造成过多的时间浪费，可能用 watchEffect 就不能做到这一点

不对，听说 watchEffect 也能做到这一点，问了半天 ai 还是问不出问什么不建议把防抖用在 watchEffect 上面，还是先来研究一下防抖 debounce 这个具体函数吧

原来甚至防抖这个函数都不是 vue 或者 js 自带的，而是自己手写的，有意思

在研究手写 debounce 函数的时候，碰到一个...args，听说这个和 arguments 基本相似，但是好像不完全是同一个东西，arguments 是一个类对象数组，而 args 就是一个数组，而 arguments 是 function 才有的，箭头函数是没有的，但箭头函数可以用 args，所以感觉这个 args 比较好用

其实防抖的原理很简单啊，就是搞个延时器，然后给一定的缓冲时间，每次创建函数的时候就清除上一个函数创建的延时器，让它不要继续执行逻辑，这样就能保证只执行最后一次的逻辑，具体的代码如下

```js
const debounce = (fn, delay) => {
  let timer = null;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn(...args);
    }, delay);
  };
};
```

顺便说一下，这里的...args 是自己定义的参数，不是像 arguments 这种自带的，我还是先用着箭头函数吧，function 的 this 指向我还是搞不明白

这里还有一个防抖的实例程序，没事可以玩一下

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h2>输入框防抖演示</h2>
    <input type="text" id="search" placeholder="输入内容试试..." />
  </body>
  <script>
    const debounce = (fn, delay) => {
      let timer = null;
      return (...args) => {
        clearTimeout(timer);
        timer = setTimeout(() => {
          fn(...args);
        }, delay);
      };
    };

    const searchAPI = (value) => {
      console.log("发送请求:", value);
    };

    const debouncedSearchAPI = debounce(searchAPI, 500);

    const input = document.getElementById("search");
    input.addEventListener("input", (e) => {
      debouncedSearchAPI(e.target.value);
    });
  </script>
</html>
```

ref\<xxx\>就是创建一个包含类型为 xxx 的值的响应式对象

ok，我看了一下，一开始的 script 代码就是搞一下设置主题这些玩意

```js
const arr = [0, 1, 2];
arr.name = "Jason";
arr["name"] = "Jason";
```

这两段代码是等价的，把数组 arr 打印出来的结果是这样的

```js
[ 0, 1, 2, name: 'Jason' ]
```

实际上用非数字下标，就是创建了一个新的属性，然后本身 array 就是一个对象，所以这样逻辑上就很合理，然后这个新的属性并没有被认为加入到数组当中，所以 arr[3]是访问不到 Jason 这个属性的

再来学一下 interface 吧，这玩意我看在项目里面也经常用到

interface 它大概是长这个样子的

```ts
interface User {
  name: string;
  age: number;
}

const u: User = {
  name: "Jason",
  age: 21,
};
```

有点像是 c++的结构吧，但是原理上还想不太一样，更像是一个宏定义，但是它的功能多一样，就是它是 ts 用来规范一个对象的属性类型，然后它不会生成实际的代码，而是在编译成 js 之后变成简单的源代码，所以其实是一种语法糖？ts 主要干的事情就是规范属性的类型，增加可读性

还可以进行继承

```ts
interface Person {
  name: string;
}

interface Student extends Person {
  school: string;
}

const s: Student = {
  name: "Jason",
  school: "PKU",
};
```

很简单，和其他语言的一样，不过我很少用继承这个功能所以我这里稍微说一下，继承会包含原来的属性，不需要显式地写出来，但是这个属性还是会在子类当中存在，然后在子类当中定义的属性是附加的

还可以定义成员函数

```ts
interface Add {
  (a: number, b: number): number;
}

const add: Add = (x, y) => x + y;
```

加上一个括号就代表是函数了

说一下，js 对象当中是允许带有属性的，但是 JSON 对象当中是不允许的，所以如果将一个带有成员函数的 js 对象转换成一个 JSON 对象的话，带有函数的那个属性是会直接被删掉的

如果有仔细看的话，会发现这个 interface 的唯一属性是没有名字的，这个叫可调用接口，实际上要用的话，就直接像普通函数那样用就行了，但是我很疑惑这和普通函数有什么区别

```ts
interface Fn {
  (x: number): number;
  desc: string;
}

const myFn: Fn = (x) => x * 2;
myFn.desc = "这是一个函数对象";

console.log(myFn(5)); // 10
console.log(myFn.desc); // 这是一个函数对象
```

甚至还可以这么写，我以为只有一个函数属性的 interface 才叫可调用接口，没想到多个属性也可以，可以直接当成函数调用，然后也可以指定属性

```ts
function add(a: number, b: number): number {
  return a + b;
}
```

普通函数在 ts 当中也是可以指定返回值类型的，所以 interface 的作用不是为了限制返回值类型，一个比较重要的功能我认为是，提供了一个函数类型的框架，我给个例子

```ts
function useFn(fn: Add) {
  return fn(1, 2);
}
```

这里的 Add 是一个 interface，然后它指定了函数的框架，在这里我限制传进来的函数类型一定要是 Add 的，这样我就可以方便地限定这个函数的结构

然后是数组的部分

```ts
interface StringArray {
  [index: number]: string;
}

const arr: StringArray = ["a", "b", "c"];
```

[]就是指定为数组

index 是执行这个数组下标的类型，因为 js 数组是允许使用非 number 类型作为下标的，这里 ts 限定了一下下标的类型

那么这里也是一样，可以直接当成数组使用的

对了之后我还得学一下数据库的索引，一面的时候面试官问了我，我现在才想起来

要在本地运行 ts 代码，要安装 ts-node

```bash
pnpm install -g ts-node
```

发现我连 typescript 也没有安装，然后我用 pnpm 安装出了一点问题

```bash
D:\coding>pnpm install -g typescript
 ERR_PNPM_NO_GLOBAL_BIN_DIR  Unable to find the global bin directory

Run "pnpm setup" to create it automatically, or set the global-bin-dir setting, or the PNPM_HOME env variable. The global bin directory should be in the PATH.
```

听说在控制台输入一下 pnpm setup 就可以解决这个问题的，原因是这里还没有设置 pnpm 的全局 bin 目录，记得输入完指令之后要重新启动控制台

话说这个 pnpm 的安装完成的信息描述有点帅的，很简洁，不知道是哪个大神开发的这个玩意，可以查一下

如果在全局安装了 ts-node 之后发现 cmd 可以访问 ts-node 但是 vscode 不行，那就是 vscode 还没有同步到系统的最新环境变量，将 vscode exit 然后重新启动就行，不是叉掉，这样是不行的

type 也是 ts 的语法，大致上的使用和 interface 很像，细节可能要在实际使用当中才能体会到

给一段代码，综合体验一下 type 的使用

```ts
type A = {
  name: string;
};
type B = {
  age: number;
};

const obj: A & B = { name: "Jason", age: 20 };
console.log(obj);
```

A & B 就是两个类型都要，|是两者其一，有点神奇吧，但是思考一下发现逻辑上就应该是这样的

跳出指定回圈

```js
var num = 0;
outPoint: for (var i = 0; i < 10; i++) {
  for (var j = 0; j < 10; j++) {
    if (i == 5 && j == 5) {
      break outPoint; // 在 i = 5，j = 5 时，跳出所有循环，
      // 返回到整个 outPoint 下方，继续执行
    }
    num++;
  }
}

alert(num); // 输出 55
```
