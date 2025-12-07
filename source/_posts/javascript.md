---
title: javascript
date: 2025-10-20 23:37:58
tags: javascript
---
# 预防事件预设动作(跳转之类的)

```js
function func(event){
    event.preventDefault();
    ...
}
```

# dom的getbyId函数

```js
document.getElementById("{$id的名字}")
```

# FormData类

```js
var form=document.getElementById("submit_form");
var formData=new FormData(form);
```

### FormData获取数据

```js
var data=formData.entries();
```

# 字符串相关函数

str.trim(); // 左右两边都去掉空格

return str===undefined || str===null || str.trim()===''; //字符串判空条件

str.length; //不是函数

# fetch请求

```js
//fetch请求
fetch(url,{
    method: "POST",
    body:formData
}).then(response=>response.json())//返回值变成json格式
.then(data=>{
    console.log(data)
})
.catch(error){
    console.log("error: "+error) 
}
```

# 表单

表单项要有name,不然不会被FormData对象侦测到

# 网页跳转

```js
window.location.href="...";
```

# 创建标签

```js
//创建表格及表头
var table=document.createElement("table");
var thead=document.createElement("thead");
var tr_arr=["名字","作者","语言"];
for(var i=0;i<3;i++){
    var tr=document.createElement("tr");
    tr.innerHTML=tr_arr[i];
    thead.appendChild(tr);
}
table.appendChild(thead);
var container=document.getElementById("container");
container.appendChild(table);
console.log(container);
```

# 给元素加上class属性

```js
// 假设你想给一个id为"myElement"的元素添加一个class "new-class"
var element = document.getElementById("myElement");
element.classList.add("new-class");
```

# 插入到某元素前面

```js
var add_btn=document.getElementsByClassName("add_btn")[0];
container.insertBefore(table,add_btn);
```

# 获取触发事件的对象

```js
function del_btn_handle(event) {
    console.log("删除按钮事件触发");
    console.log(event.target);
}
```

# 获取元素的父节点

```js
var tr = del_btn.parentNode;
```

# post了json数据,后端如何获取数据

```php
$json_data = file_get_contents("php://input"); // 获取到的是json格式的数据
$data = json_decode($json_data, true); // true代表转换为数组
```

