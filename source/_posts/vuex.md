---
title: vuex
date: 2025-10-20 23:35:47
tags: vuex
---
# vuex

我是理解为一个全局变量管理器, 里面都是全局变量和全局函数

```javascript
export default {
    namespaced: true,

    // 全局的状态初始化
    state: {
        count: 1,
    },

    // 计算state, 获取对应的值(计算属性?)
    getters: {
        countStatus(state) {
            return state.count >= 1
        }
    },

    // 更新状态的方法, 更新state的唯一方法, commit mutations
    mutations: {
        setCount(state, num) {
            state.count = num
        }
    },

    // 可以异步操作, 可以返回promise
    actions: {
        setCountPromise(context, num) {
            return new Promise((resolve, reject) => {
                if (num > 100) {
                    reject("不能大于一百")
                } else {
                    context.commit('setCount', num)
                    resolve()
                }
            })
        }
    },
}
```

```javascript
import { createStore } from 'vuex'
import number from './state/num.state.js'
import uInfo from './state/userInfo.state.js'

export default createStore({
    // 分模块, 类似于头文件
    modules: {
        number,
        uInfo
    }
})
```

# 路由守卫

处理页面跳转时的事件

```javascript
// 路由守卫
router.beforeEach((from, to, next) => {
  // 判断用户是否登录
  console.log("store", store.state.uInfo)
  const uInfo = store.state.uInfo.userInfo
  if (!uInfo.userName) {
    // 未登录, 跳转到login页面
    if (from.path === "/login") {
      next() // 一定要有next()
      return
    }
    next("/login")
  } else {
    next()
  }
})
```

上面是一个判断用户是否登录的逻辑, 如果用户已登录, 那么就跳转到想跳转到的页面, 如果未登录, 则需要跳转到login页面, 需要注意的是, 需要判断当前页面是不是已经是login页面, 如果是就跳一次就结束, 不太清楚为什么一定要有next()



# 调用mutations(用来更新数据的函数集)

一段更新函数调用实例

```javascript
// 第一个参数是函数名, 后面的参数跟第一个参数所对应的函数的参数一致
store.commit('setUserInfo', data.loginData)
```

## 路由跳转

```javascript
router.push({
                path: "/users"
            })
```

# 登录状态存储(localStorage的使用)

刷新的时候, store里面的全局变量都会被重新初始化

一段localStorage的setItem使用实例

这个setItem应该是把数据转换为JSON的格式字符串

```javascript
// 参数: (key, value)
// 注意value的类型只能是字符串
localStorage.setItem("loginData", JSON.stringify(data.loginData))
```

获取数据使用getItem

```javascript
// 第一个参数也是key
userInfo: localStorage.getItem("loginData")
```

下面是一段完整的操作, 因为getItem的返回值有可能是NULL(就是没有赋值)

不是很能看得懂这段操作, 如果getItem的返回值是NULL, 那么&&后面的语句将不会被执行

所以他想干的事情是并上一个{}变成空集?, 那么&&和||符号的含义其实是集合当中的交集并集的意思吗

但是这个语句难道不是返回一个布尔值吗

null和{}是有本质区别的, null不是一个对象, 而{}表示一个没有任何属性的对象

给一个对象赋值为null是会报错的

```javascript
userInfo: (localStorage.getItem("loginData") && JSON.parse(localStorage.getItem("loginData")))
            || {}
```

上面那个太难理解了

我觉得下面这个要好理解的多

```javascript
userInfo: localStorage.getItem("loginData") == null ?
            {} : JSON.parse(localStorage.getItem("loginData"))
```

## localStorage的存放位置

在f12里面的应用的本地存储空间

英文的话就是application的localstorage



## setup里面声明的函数要暴露出来(记得要return)

```javascript
setup() {
        const logoutHandle = () => {

        }
        return {
            logoutHandle
        }
    }
```

## useRouter和useStore必须在setup的作用域内使用, 否则返回undefined