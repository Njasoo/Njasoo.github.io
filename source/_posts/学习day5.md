---
title: 学习day5
date: 2025-11-15 10:41:11
tags: ["css", "tailwindcss", "vue", "django"]
---

# 走了一堆弯路

在安装 tailwindcss 的时候，还叫我安装上另外两个库，一个是 postcss，另一个是 autoprefixer，听说其实 tailwindcss 和 autoprefixer 是 postcss 的插件，也就是说 postcss 才是最主要的，然后这个 postcss 就是用来处理 css 的编译和优化，其实基本上就是把它装上就可以了，其余的什么都不用配置了，autoprefixer 好像是在样式前面加上一些兼容前缀使得样式在各种浏览器上面都能运行，总之安装上就行了，我最主要要用的就是 tailwindcss

```bash
npx tailwindcss init -p
```

这里说一下 npx 是运行本地环境下的库的意思，这里改成 pnpm 会报错，因为我没有在全局安装 tailwindcss，-p 这个命令的意思是在生成 tailwind.config 的前提下同时生成 postcss.config.js

哎，pnpm 有点难搞，我还是先用着 npm 比较好

一件真重要的事情，不要在 venv 的虚拟环境里面使用 npm，虚拟环境会覆盖实际的环境变量，然后找不到 npm，开两个控制台就好了，在使用 npm 命令的时候，随时注意是不是在虚拟环境当中

卧槽碰到一个报错搞了我大半天，我安装 tailwindcss 的时候一直没有装上，我以为是 pnpm 的问题，结果是 tailwind 貌似最新版不兼容 vite，也就是 v4 的版本，要装上 tailwindcss 的 v3 版本

```bash
npm install -D tailwindcss@3
```

这个命令的@3 会自动装上大版本为 3 的最新版本

顺便说一下，tailwindcss 是开发时的依赖，我想要用回 pnpm 重新安装，这样可以省一点空间

```bash
pnpm exec tailwindcss init -p
```

如果用的是 pnpm，建议 pnpm exec 来取代 npx，其实应该是 pnpm dlx 对应 npx 的，但是这里我的项目里面不知道为什么用 pnpm dlx 发生了报错，所以我改用了 pnpm exec，这几个命令就是用来执行安装到项目当中的依赖里面的一些脚本

# 实际上最新版本的 tailwindcss 配置方法

然后我就又发现，我看的是旧的文档，妈的，原来还有官网会保留旧版本的文档的，艹，我决定重新安装最新的版本

```bash
npm install tailwindcss @tailwindcss/vite
```

新版本还不用安装这么多的狗屎，剩下的操作慢慢看文档就好了，这里强调一件事，npm 和 pnpm 不要混用

tailwind 要在一个 css 文件里面@import "tailwindcss"，然后在 main.ts 里面引用，这样才能使用 tailwind 的类，可以把默认的 style.css 里面的内容全部删掉，然后只写一句代码，看起来挺怪的

# Tailwind CSS IntelliSense

一个用来自动补全 tailwindcss 代码的 vscode 插件

这个插件甚至可以补全组件库的类名

```json
"tailwindCSS.includeLanguages": {
    "html": "html",
    "javascript": "javascript",
    "css": "css"
},
"editor.quickSuggestions": {
    "strings": true
},
```

在 settings.json 里面加上这一段代码之后，就可以在全局的任何地方都是用上 tailwindcss 的自动补全功能，感觉 AI 越来越不行了，老是告诉我一些没有用的或者很旧的信息，还是看博客和文档比较靠谱

cotainer 和 mx-auto 这两个类通常是配套使用的，我试了一下 container 在窗口改变大小的时候，不会占满整个屏幕，而是根据窗口的大小，改变成几个固定的大小，然后 mx-auto 功能就很简单了，margin-left: auto 和 margin-right: auto，就是置中，通常用在最外层，因为我们也不会把 body 改成 flex 之类的容器，所以 mx-auto 是生效的

# daisyUI

一个基于 tailwindcss 的组件库，安装挺方便的，参考文档去安装，没碰到什么问题

卧槽，tailwindcss，虽然我不知道实际开发的时候，会不会碰到什么问题，但是我现在用的时候，感觉引入类名实在是太方便了

# 填满屏幕

min-h-screen 这个类比 h-[100vh]好用

# 基本的 UI 设计

我观察了一下知乎的首页设计，发现它的整体色彩都是非常一致的，基本上都是白色，现在的设计逻辑基本上都是往简洁的方向走，每个组件都是白色，就连顶部的导航栏都是白色，再加上淡淡的阴影来进行区分，然后最底层的背景就是淡淡的灰色，我按照这种风格改良了一下我的页面，发现确实顺眼很多，但是我每个组件之间的间距隔得有点太开了, mt-3 还行

# 题外话

感觉详细地做笔记是很有必要的啊，今天在查 cors 和 axios 怎么用的时候，发现自己之前写的笔记都能用上，节省了我好多时间，我现在对于笔记的观点是这样的，可能大部分情况下，做过的笔记都不会再看，但是有 10%的情况需要看以前的笔记，这一部分的时间可能节省了我 90%的时间，嗯，我瞎说的，反正做笔记挺好的

# 前端 axios get 请求带上参数怎么写

```ts
import request from "./request";

export const getHotItems = (source?: string) => {
  return request.get("hot/", {
    params: {
      source: source,
    },
  });
};
```

这个文件的格式是 ts，为了格式的一致性，request 建议也写成 ts 后缀，尽管它里面还没有用到任何 ts 的语法，好，这里这个代码我解释一下，source?意思就是 source 这个参数是可传可不传的，params 这里对应后端 request.GET，get 的参数就是放在这里的，记得代码要加上 return，axios 的接口都是要返回一个 promise 对象的

# 本地后端的域名前缀

是 http 不是 https，我记得之前好像不是这样的，但是感觉 http 比较合理

# 再次重申 ref 的作用

ref 和 reactive 目前在我眼里最大的不同就是，ref 可以整体重新指向，而 reactive 是不能的，在什么情况底下这是有用的？就是在网页加载的时候我要请求一个数据列表存到一个响应式变量里面，这个变量一开始是空的，然后我通过接口请求数据，要给这个列表赋值，这个时候我就需要把它给整个全部重新赋值

# 记录一下第一次在项目里面用上了 interface

```ts
interface NewsItem {
  id: number;
  title: string;
  source: string;
  crawl_time: string;
  rank: number;
}
const newsList = ref<NewsItem[]>([]);
```

这里用 type 也是可以的，但是莫名其妙地感觉 interface 好像是更常用的东西，我看过几次别人的项目都是用的 interface，可能 interface 有些继承的特性比较好用吧，而 type 是用&来连接两个类型，这个逻辑上不是太清晰（可能）

# 使用 watch 监视响应式变量式的参数类型

```ts
watch(selectedValue, (newVal: string) => {
  ...
})
```

这里我 watch 了一个响应式变量 selectedValue，然后函数参数是 newVal，这个 newVal 不是响应式变量，是当前这个响应式变量的原始值，就是可以直接拿来用

# 关于知乎的一个小知识

```py
url = f"https://www.zhihu.com/question/{target.get('id')}"
```

知乎的热榜接口并没有直接放上问题的连接，而是给了问题的 id，而我们可以根据这个格式来访问到具体问题的 url，从这个格式看来，zhihu 似乎遵循了 restful api 接口的命名规范

# b 站热搜 url 找不到怎么办

我看了一下 b 站热搜接口的字段，没有发现 url，发现了一个 resource_id，但是仔细看了几个之后发现都是 0，那这个根本就没法用，不过有一个很简单的解决方案，就是直接拿标题去搜

```py
url = f"https://www.bilibili.com/search?keyword={title}"
```

而实际上，我们要的也正是这个页面，然后我观察了一下这个 url 的搜索格式，发现好像和 django-filter 是一样的，不是说 B 站的搜索是用 django-filter 做的，而是说 django-filter 能做到同样的效果，但是我相关的笔记在博客上面还没有，等下次有机会的时候再写笔记

# URLField 和 CharField 的区别

其实两个都是存字符串类型的，但是 URLField 会检查存进来的字符串是否符合 url 的规范，然后 URLField 默认 max_length=200，而 CharField 必须要指定 max_length。max_length=200 已经够用了，再长可能也会出现一些兼容性问题，所以基本上也不用给参数，然后补充一下 sql 并没有 url 这个数据类型，它只是 django 搞出来的一个提供数据验证的类型罢了

# 目前不知道 ts 的作用是什么

上面有提到说在项目里面用到了 interface，但是运行之后发现它根本没有做类型检查，然后一查跟我说 ts 只在编译阶段做检查，运行阶段是不会做检查的，那感觉这个 ts 的应用范围很小啊，因为前端很多数据都是从后端拿到的，这种时候肯定都是运行阶段的，那编译阶段能检查到的数据都是自己定义的一些字面量，这种情况少之又少，目前来看感觉可能不是为了代码规范的才使用 ts，而是有一些比较好用的特性所以才用 ts，比如 interface?哦还有写代码的时候给合作人员说一下这个数组的类型是什么这样，但是实际运行没有任何的用处

# 连续对同一个 url 前缀 path 是不会覆盖的

```py
urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/", include("hotItem.urls")),
    path("api/", include("source.urls")),
]

```

只是单纯提醒一下自己，仔细想想都会觉得覆盖太不合理，但是自己已经有至少两次这样的疑问了

# vue 临时公网 IP 暴露

在输入 pnpm dev 命令的时候，会显示一个 Local 的 ip 地址，但是一直没怎么看过一下其实还有一个 Network 的 ip，一般情况下，它是不显示出来的，需要加上--host 运行参数，加上之后，出现了以下的东西

```bash
➜  Local:   http://localhost:5173/
➜  Network: http://192.168.56.1:5173/
➜  Network: http://192.168.134.1:5173/
➜  Network: http://192.168.220.1:5173/
➜  Network: http://10.22.44.62:5173/
```

四个 Network ip 里面其实只有最后一个是能用的，那么前三个到底是什么，前三个好像都是我安装虚拟机的时候自动创建的虚拟网卡，用在主机和虚拟机之间的通讯，第四个才是真正和外网连接的 ip 地址，而且前三个 ip 长的就很像本地的 ip 地址，看上去就完全不能用

# 暂时部署项目的完整教程

上面已经解决了前端的访问问题，然后前端拿不到后端的数据，首先就是后端不能再监听 localhost:8000，要监听上面的 10.22.44.62:8000，或者最方便的，直接监听 0.0.0.0:8000，具体的命令就是这样

```bash
python manage.py runserver 0.0.0.0:8000
```

前端的 axios 请求的 baseURL 也要改成 10.22.44.62:8000/api，django 的 settings 里面也要添加 ALLOW_HOSTS，具体大概是这样的

```ts
import axios from "axios";

const temporary_BASE_URL = "http://10.22.44.62:8000/api/";

const request = axios.create({
  // baseURL: "http://127.0.0.1:8000/api/",
  baseURL: temporary_BASE_URL,
  timeout: 5000,
});

export default request;
```

这是前端 axios 的配置部分

```py
ALLOWED_HOSTS = ["localhost", "127.0.0.1", "10.22.44.62"]
ALLOWED_HOSTS = ["*"]
```

这是后端的部分，不过其实 localhost 和 127.0.0.1（虽然是同一个玩意）默认就是被允许通行的，不用写也行，第二种放行的方法也是可以的，但是感觉太不安全了，我都用了数据库了，怕被别人用 sql 注入攻击，不过我都用 ORM 了，应该很难发生什么问题

然后这样搞完一轮之后，手机上也能暂时访问到整个项目了

# 对一个项目的总结

基本上的功能都搞定了，因为也是一个很简单的项目，但是涉及到的技术栈还是有点广的，但是深度不够，我会想办法在这上面添加更多的功能，而且我感觉一个项目要首先让别人看起来很牛逼，需要在美术这方面下点功夫，不能再用我自己那种蹩脚的审美来设计项目了，我可能素描挺好的，但是色彩搭配这方面做得很差，我以前学画画的时候就经常有这种感觉，我的草稿画的挺好的，结果上色之后，反而没那么好看了，我感觉这次的项目是有点小用，有用在于好像没见过类似的项目，但是功能方面确实也就那样，我后续可能还要再添加一些功能，比如数据化可视之类的，搞更多的功能就能搞分页，然后就能整上 vue-router 了，我明天应该主要先研究怎么部署前后端

# 免费部署全栈项目的方案

前端 vercel，后端 Render，Github actions 可以自动跑我的爬虫脚本，感觉不赖，但是还没有实际的去尝试
