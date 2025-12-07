---
title: 学习day9
date: 2025-11-19 12:16:03
tags: ["vue"]
---

# vue-router 注意事项

children 写的是相对路径，redirect 最好写绝对路径

# props 的问题

```ts
interface Props {
  selectedValue: string;
}
const props = defineProps<Props>();
```

规定 props 的类型限制的 ts 写法，另外这里这个 selectedValue 我父组件那边传的是响应式变量，但是传过来的时候，会自动解包把里面的值拿出来，所以这里写 string，是限制 ref 里面的值的类型

```ts
watch(
  () => props.selectedValue,
  (newVal: string) => {
    ...
  }
)
```

这里因为 props.selectedValue 是一个普通值了，所以写一个 getter

# 子路由传值

```html
<router-view v-slot="{ Component }">
  <component :is="Component" :selectedValue="selectedValue"></component>
</router-view>
```

component 是 vue 内置的动态组件标签，:is 用来绑定一个组件对象,{Component}是解构的组件对象

# vue keyup 事件触发

```html
<input
  type="text"
  v-model="searchText"
  placeholder="请输入搜索内容"
  class="input input-bordered join-item grow"
  @keyup.enter="searchHandle"
/>
```

# 动态附加 class

```html
<span
  v-for="item in pages"
  :key="item.name"
  class="hover:text-black hover:font-bold hover:cursor-pointer"
  :class="
    selectedPage == item.value
        ? 'border-b-5 border-blue-500 text-black font-bold'
        : ''
    "
  @click="selectPageHandle(item.value)"
></span>
```

:class 这里的样式都是在原 class 的基础上继续附加的

# 访问 github 仓库文件

要访问 raw 的 url，普通 url 返回的是普通的 html 文件

# interface 套 props 的简约写法

```ts
const props = defineProps<{
  selectedValue: string;
}>();
```

和上面的写法是等价的

# 导航栏和 url 对不上

不要自己维护选中了什么路径，用 router.currentRoute.value.path 来查询

# src 直接绑定 base64 字符串可以直接显示图片

这个 base64 字符串建议用 text 来存，太长了

# 外键获取属性

```py
from rest_framework.views import APIView
from .models import WordCloud
from rest_framework.response import Response
from rest_framework import status


class WordCloudAPIView(APIView):
    def get(self, request):
        source = request.query_params.get("source")
        url = WordCloud.objects.get(
            source__value=source
        ).url  # 实际上source关联的还是对象，要这样获取列名
        return Response({"url": url}, status=status.HTTP_200_OK)

```

这里 source 是个外键，虽然定义时写了 to_field="value"，但是其实还是指向一个对象，不是字符串，所以要用 source\_\_value 去找外键的属性值

# 生产环境刷新页面 404

改成 createHashHistory 就可以，原理好像是#后面的 url 不会被请求，只会请求域名，另一个解决方法是配置 nginx
