---
title: axios
date: 2025-10-20 23:36:17
tags: axios
---
const用于定义常量, let定义变量



## bootcdn.cn

国内的镜像网站, 存了一些常用的前端包的链接, 速度比较快



# querySelectorAll

```javascript
const btns = document.querySelectorAll('button')
```

把所有button标签的对象聚在一起变成数组, 显然, 顺序是从上到下



# promise对象

用于异步处理的对象, 不是很懂, 说是代表了某个未来才会知道结果的对象

总之一个函数若能够返回一个promise对象, 那么就可以在函数后面加上then来进行成功或失败时的回调函数

```javascript
// 第一个按钮绑定事件
btns[0].onclick = function () {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'GET',

        // URL
        url: 'http://localhost:3000/posts/2' // 访问id为2的post
    }).then(response => {
        console.log(response)
    })
}
```

# export 关键字

用来暴露某个变量,使得其他文件可以引用这个暴露之后的变量



## 传入参数的属性名称一定要跟接口参数的名称相同啊

## redire的路径不能和path路径相同, 不然就会无限递归

获得token之后还要再刷新一下



# element分页设计

layout="prev, pager,sizes,next, jumper, ->, total" 

sizes是每页显示的条目数量



## v-model是双向绑定,v-bind是单向绑定属性值