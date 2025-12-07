---
title: 学习day4
date: 2025-11-14 14:46:31
tags: ["爬虫", "python"]
---

很久之前就学过爬虫了，但是只会用，不知道原理是什么，现在来简单地学习一下吧。

User-Agent 是爬虫的时候必须提供的请求头里面的一个字段，告诉服务器我的设备或浏览器类型，不提供这个的话，服务器一看就知道我不是人类，就会拒绝响应

www.zhihu.com/billboard，这个是知乎的热点头条API，要爬虫的话，从这里爬会比较轻松一点

```py
soup = BeautifulSoup(html, "lxml")
```

这个 lxml 是目前最快的 html 解析库，听说是爬虫最常用的一个库，也可以用默认的 html.parser，是 python 自带的

我在爬知乎的热度榜的时候发生了失败的情况，居然还要加上 Cookie，知乎也是做了一点初级的防爬机制的

还有编码的时候要把 encoding 改成 utf-8，不然会乱码

```py
soup = BeautifulSoup(res.text, "lxml")
print(soup)
with open("test.txt", "w", encoding="utf-8") as f:
    f.write(soup.prettify())
```

这个 prettify 方法就是解析之后的 html 树进行一个美观化的操作，不会改变实际的内容，就是加点缩进，原本是没有缩进的，全部靠左对齐的

```py
a_tags = soup.select("td.td-02 a")
```

这个 select 方法挺好用的，空格表示后代，逗号表示并列的关系，类名记得加上.号，比 find 或者 find_all 要好用

这次要做的这个各大平台热榜数据可视化的系统，为什么要把爬虫得到的数据放在数据库里面的，原因是如果我直接从前端去访问 api 大概率是会被跨域拦截的，同时前端是没有办法去调用爬虫程序的，但是后端可以，这也就是后端的作用

还有一个问题就是，我后端可以调用爬虫程序，那我干脆爬完之后不存到数据库里面，我直接就是返回给前端行不行，理论上可行，但是实际上，每次前端调用数据的时候，后端都要爬一次，过度频繁地爬虫可能会导致我被网站封 IP，这样太危险了，所以比较合理的方式是我在后端每天爬一次，然后存在数据库里面，等前端要用这些数据的时候我再从数据库里面拿出来，还有一个原因就是爬虫的速度太慢了，从数据库拿会比较快

python manage.py xxx.py 用这个命令去跑一个脚本程序和直接用 python 跑脚本程序的区别就是第一个命令会提供 django 配置好的环境，比如 orm 和 settings 里面的东西，其实最重要的就是 ORM，我可以在脚本里面操纵数据库

在 python 的函数里面修改一个变量的时候，它默认都是局部变量，如果使用的是全局变量，需要加上 global 关键字，没有加的话，能访问，但是不能修改

```py
import requests as rq
import json
from bs4 import BeautifulSoup

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36",
    "Cookie": "SUB=_2AkMeSm12f8NxqwFRmv0WzGPmb49-zgjEieKoFpytJRMxHRl-yT9yqlUjtRB6NcpDmXB8v7jCDdUOANFsLEoZJwuDT4Wu; SUBP=0033WrSXqPxfM72-Ws9jqgMF55529P9D9W5Ip-3.Qjwa_aD0wH30D.qZ; _s_tentry=passport.weibo.com; Apache=8441114877984.956.1763107391573; SINAGLOBAL=8441114877984.956.1763107391573; ULV=1763107391574:1:1:1:8441114877984.956.1763107391573:",
}


def fetch_weibo_hot():
    url = "https://s.weibo.com/top/summary?cate=realtimehot"
    res = rq.get(url, headers=headers)
    res.encoding = "utf-8"
    soup = BeautifulSoup(res.text, "lxml")
    a_tags = soup.select("td.td-02 a")
    cnt = 0
    res = []
    for a_tag in a_tags:
        new_obj = {"title": a_tag.string, "rank": cnt, "source": "weibo"}
        res.append(new_obj)
        cnt += 1
        if cnt == 10:
            break
    return res


if __name__ == "__main__":
    res = fetch_weibo_hot()
    print(res)

```

现在终于知道 if 条件那段代码的作用是什么了，当上面这个 py 文件作为模块被其他文件引用的话，无论是否用到，模块最顶层的代码都会被执行一遍，如果加上 if 条件的话，就不会被执行

ok，新发现，python 文件引用的时候，会从两个方面去找，第一个是基于当前文件的路径位置，第二个就是 sys.path，所以如果自己在 sys.path 加上了新东西，就可以根据 sys.path 里面的路径来引入文件，python 都能识别的，顺便说一下 sys.path 这个不叫环境变量，这个叫做模块的搜索路径范围（大概是）

```py
sys.path.append(os.path.abspath("../backend"))
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "backend.settings")
django.setup()
```

这里我想干的事情是在外部文件（项目外面的文件）里面使用后端项目里面定义的模型，所以需要进行一些配置，才能使用，第一行代码就是添加这个模组的搜索范围，然后第二行才是这个这个环境变量，记得这个 key 一定要叫 DJANGO_SETTINGS_MODULE，这是规定的，搞完之后就可以 setup 了，然后就可以在这个脚本文件里面使用 ORM 了

```py
from weibo_spider import fetch_weibo_hot
from zhihu_spider import fetch_zhihu_hot
from bilibili_spider import fetch_bilibili_hot
import os
import django
import sys

sys.path.append(os.path.abspath("../backend"))
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "backend.settings")
django.setup()

from hotItem.models import HotItem

if __name__ == "__main__":
    res_weibo = fetch_weibo_hot()
    res_zhihu = fetch_zhihu_hot()
    res_bilibili = fetch_bilibili_hot()

```

一个仔细想想也能想到的问题，要加载完之后才能引入模型，不然就找不到了，但是 vscode 没有报错，所以我以为是可以的

```py
current_file_path = os.path.abspath(__file__)
BASE_URL = os.path.dirname(os.path.dirname(current_file_path))
sys.path.append(BASE_URL)
# assert False
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "backend.settings")
django.setup()

```

这样写才是对的，上面给的关于 django 配置环境变量的代码，路径那部分是基于工作目录的，那是不对的，这里看代码应该挺好懂的，os.path.dirname 是文件夹或者文件所在的目录名，sys.path.append 要穿的参数就是一个绝对路径，current_file_path 这个不用解释了
