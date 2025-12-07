---
title: 学习day6
date: 2025-11-16 11:56:12
tags: ["部署"]
---

# 外键赋值

很好今天一来就碰到问题，我在跑脚本往数据库里面添加数据的时候报错了

```py
HotItem.objects.create(
    title=x["title"], rank=x["rank"], source=x["source"], url=x["url"]
)
```

原本的代码是长这样的，其中 source 这个字段是一个外键，指向 Source 表的 value 字段，我以为可以直接给一个字符串就行了的，没想到居然要给一个对象实例，如果要这样的话，我就要通过查询找到 Source 表当中 value 为比如说"weibo"的对象，然后再把它赋给 source，这样略显麻烦，另外一种方法就是下面这种

```py
HotItem.objects.create(
    title=x["title"], rank=x["rank"], source_id=x["source"], url=x["url"]
)
```

通过在 source 字段这里加上\_id，这样我就能给它赋上一个字符串，然后它自己回去找对应的对象是哪个，这样我就不用引入另外一个 model 进行查询了，只能说是稍微方便一点，这个语法是 django 特有的，不是 sql 的语法

# 后端部署教程

ok 折磨半天，终于部署好了 django 后端，但是步骤实在是太多，而且中间有很多细节，也碰到了很多小 bug，我不方便一边做一边做笔记，首先是我当时不知道每一个操作是不是都真的有用，所以如果把所有的步骤都记下来的话，会有很多冗余的信息

## 首先是部署数据库

在 render 部署的时候我一开始是选了 web service，这个服务就是用来部署全栈或者后端服务的，现在我还没有把前端也部署了，我感觉在 render 上面部署也挺好的，待会再考虑吧，ok，当时我想部署的时候，我在想要不数据库就直接用 sqlite3 就算了，因为我的数据量也不是很大，我直接用它也没什么影响，但是发现是不行的，这是为什么呢，因为 sqlite3 是基于文件的数据库，它的数据都是存在文件里面的，但是 render 部署的时候，是会创建一个临时的容器来保存我的代码，而我的数据都存在文件里面的话，下次部署，就会生成新的容器，那么我的数据就会消失，虽然说我的数据都是每次通过爬虫重新获取，问题也不大，但是从长期的角度来说用 sqlite3 是绝对不行的，刚好 render 上面也可以免费使用 postgresql 的服务器，至于这个 postgresql 和 mysql 有什么不同，其实不是很重要，反正查询的语法都是一样的，介绍完为什么要用 postgresql 的背景之后，就开始讲怎么注册了

就在 render 官网上面选择 postgresql 的服务，然后进去，要填的东西不多，首先是这个 name，随便填一个域名-db 好了，其他的都不用填，按照默认的来就行了

然后 create 完之后，能看到左上角有个 connect，里面有两个 url，一个是 internal url，另一个是 external url，这两个都能用，external url 就是在哪都能用，而 internal url，如果后端也是同样部署在 render 上面的话就建议使用这个 internal url，用 internal url 的访问速度会更快，然后这两个 url 都已经把用户名密码什么的配置都写在 url 里面了，django 那边配置数据库可以通过一个库来解析这个 url 得到相关的配置，后面会说，数据库的部分好像也就这么简单了

## 后端部署

要先做一些准备，首先是安装 whitenoise 这个库

```bash
pip install whitenoise
```

这个库的作用就是让 django 自动提供静态文件，假如说只有后端功能，其实也是会有静态文件产生的，比如说用了 django-admin 或者 drf 这个框架，都会有后台的页面，这些页面都会产生静态文件，所以这个步骤是必须要做的

```py
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',  # 放在 SecurityMiddleware 之后
    # 其他中间件
]

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

添加相关的配置，最后面那一行代码的作用是对静态文件进行压缩，加快访问的速度，可以不加

### 静态文件收集

```bash
python manage.py collectstatic
```

这个命令就是把项目静态文件、所有 app 的静态文件以及第三方库的静态文件放到 STATIC_ROOT 目录里面

具体可以这样配置

```py
STATIC_URL = "/static"
STATIC_ROOT = BASE_DIR / "staticfiles"
```

这个 STATIC_URL 其实没有用，因为我前后端是分离的，只有当用 django 写前端的时候才有用，这项配置是浏览器访问静态文件的 url 前缀，这里不详细介绍了，因为没用到，BASE_DIR 就是项目根目录，顺便说一下一个比较好的辨别根目录的方法，就是看 manage.py 的位置，manage.py 所在的目录就是根目录，再顺便说一句，下次创建后端项目的时候，还是用嵌套的方法吧，我现在前后端的文件结构有点耦合了，管理起来还是有点麻烦的

### 安装数据库相关工具库

```bash
pip install psycopg2-binary dj-database-url
```

psycopg2-binary 这个库是 postgresql 的驱动，装了才能连接到 postgresql，好像还有一个 psycopg2 的库，这个 binary 是预编译版本，其实我不知道是什么意思，听说安装比较简单

dj-database-rul 这个就非常好用了，可以直接从上面从 postgresql url 当中直接解析出字典配置

比方说 postgresql 的 url 格式是这样的，postgres://username:password@host:port/dbname，里面就包含了以下配置

```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'dbname',
        'USER': 'username',
        'PASSWORD': 'password',
        'HOST': 'host',
        'PORT': 'port',
    }
}
```

我可以通过以下代码来实现同样的功能

```py
import dj_database_url
import os

DATABASES = {
    'default': dj_database_url.config(default=os.getenv('DATABASE_URL'))
}
```

这里的 os.getenv("DATABASE_URL")是从部署之后的环境里面获取环境变量，在 render setting 里面可以填

```py
SECRET_KEY = os.getenv("SECRET_KEY", "fallback-secret-key")
```

django 的项目里面还会有这样的一个配置，默认会提供一个，我把复制下来然后添加到 render 的环境变量里面了，这个 key 的作用就是作为一个加密算法的种子，加密 cookie，token，还有加密用户密码时会用到

```py
from django.core.management.utils import get_random_secret_key

print(get_random_secret_key())
```

如果很注意安全性的话，可以这样随机生成，但是这里我没搞

### wsgi 配置

wsgi 是部署相关的，render 最后要把项目执行起来就是执行 wsgi 文件，要执行这个文件需要安装 gunicorn

```bash
pip install gunicorn
```

我们在项目的根目录加上一个 Procfile 文件，在里面写上

```txt
web: gunicorn wsgi父目录名.wsgi
```

其实我不知道是不是真的要写这个文件，这个命令我明明最后是写在了 render 的 start command 那里了，感觉其实是不用写的，下次有机会再试一下

顺手设置一下 ALLOWED_HOST

```py
ALLOWED_HOST = ["github仓库名.onrender.com"]
```

部署到 render 之后的域名默认是 github 仓库名

### render 部署

搞完前面的步骤之后，就把自己的项目搞到 github 上面，这个做过很多次了，应该不用再说，啊对了，部署的时候记得要把 DEBUG 改成 False，DEBUG 等于 True 的时候默认是让 localhost 通过的，在搞上 github 之前，先生成一个 reqirements.txt，这个文件的作用就是让 render 部署的时候自动安装需要的依赖，命令复习一下

```bash
pip freeze > requirements.txt
```

ok，开始上 render 部署了，name 随便写，哦上面说错了，域名是根据这个来的，不是 github 仓库，不过我两边都同名，想不到好名字了，先用着，其他配置不太用管

build command 我最后是这样设置的

```bash
pip install -r requirements.txt && python manage.py collectstatic --noinput && python manage.py migrate && python spiders/run_all.py
```

--noinput 的意思就是，有些时候不是输入命令之后控制台会问 Y/N 吗，这里就是不需要输入的意思，然后我解释一下后两个命令，为什么这里不需要 makemigrations，因为我已经在本地 make 好了，然后 push 的仓库了，makemigrations 的作用就是生成迁移文件，那我已经存在仓库里面了，就不用再 makemigrations 了，然后 migrate 就是去生成实际的模型，所以 migrate 一定是在我的脚本运行命令的前面的，因为我的脚本用到了数据库里面的模型，还有比较重要的一点，文件的斜杠是正斜杠不是反斜杠，windows 里面是反斜杠，但是 render 的环境好像是 linux 还是什么的

start command 这样写就行了

```bash
gunicorn backend.wsgi
```

这个就和本地的 python manage.py runserver 的作用差不多相似，肯定不完全一样，但是至少效果是一样的

其实还有一个 pre-deploy-command，但是那个要花钱才能用，我不知道全写在 build command 会有什么问题，待会问问

下面还有一个 Auto Deploy 的配置，我设置成 on commit 了，每次我的 github 仓库重新 commit 的时候，就会自动重新部署 render，我本来是有一个想法的，不是说 github actions 能定时执行一个动作吗，我就在想能不能每天 commit 一些东西到 README 里面，然后 render 那边就会触发重新部署，每次部署的时候就会重新跑爬虫脚本，这样就能实现自动更新数据库了，但是好像还是直接跑爬虫脚本比较好，时间上要节省一点，但是我还没有具体去做，不知道 github 那边跑我的爬虫代码能不能改到远程的数据库

剩下的应该就只有环境变量了，我这里就只设置了三个，DATABASE_URL，DEBUG，SECRET_KEY，其中 DEBUG 还没有用到，之后会改一下的，我现在开发环境有点炸

## 碰到的问题

中途在部署的时候碰到了一个问题，就是 url 太长了，200 长度不够存，现在改成 500 了

# 前端部署教程

想来想去，虽然其实我的前端也可以部署在 render 上面，但是我的项目是前后端分离的，所以感觉还是用 vercel 比较好，而且听说 vercel 部署很简单，不过前端部署什么的确实比后端要简单的多，无论是什么平台，然后多学一个平台的部署方法也挺好

这里说一下 vercel 只能部署静态网站，什么是静态网站，要解释这个我们反过来解释一下什么不是静态网站就好解释了，需要服务器参与渲染的前端都不是静态网站，简单的来说绝大部分都是前后端部分的项目，前后端分离的情况下，前端基本上都是静态网站

## 开始部署

vercel 会自动识别框架，而且现在我这个仓库里面有前端有后端，但是我可以设置 root directory 来定位到 fronted 文件夹那里，太方便了，而且不用像 github pages 那样我要先 build 成静态文件，然后才能部署，vercel 会自动帮我 build

npm run build 和 vite build 功能上 99%一样，但是听说一定要使用 npm run build，听说是这样的，npm run build 会自动加载环境变量和调用本地依赖，但是 vite 不会

在 pnpm 和 npm 的选择上，看本地开发用的是哪个，那部署设置就填哪一个，假如用的是 pnpm，那么项目文件上面应该会有一个 pnpm-lock.yaml，这时候一定要选 pnpm

我在这里又是搞了半天，首先是一开始我把前端和后端放在同一个仓库，导致我每次修改前端后 commit 的时候会重新部署后端，每次重新部署都要花费一两分钟才能部署成功，所以我建议还是前后端分开两个仓库存，前端部署很快的只需要几秒

### env 配置

前端访问后端的时候，有生产环境和开发环境之分，至于怎么根据不同的环境申请不同的接口呢，给个例子

```ts
import axios from "axios";

const baseURL = import.meta.env.VITE_API_URL;

console.log("baseURL:", baseURL);

const request = axios.create({
  baseURL,
  timeout: 5000,
});

export default request;
```

这里的 baseURL 是从 env 引入的，分别要在根目录底下写两个文件, .env.development, .env.production，.env 是用来放公共的环境变量，这里我没用到，知道一下就好了，前两个文件分别是这么写的

.env.development

```txt
VITE_API_URL=http://localhost:8000/api
```

.env.production

```txt
VITE_API_URL=https://hottagplatform.onrender.com/api/
```

但是你会发现前端引入的时候还是只用写 import.meta.env，因为 vite 能自动识别现在是什么环境然后去不同的文件里面去找，听说是这样的，实际上还没有测试，还有听说这些自定义的环境变量也要以 VITE 开头，不然识别不了，我也先信着，不过其实没有这个规定，用 VITE 开头定义变量也挺好的，因为有很多系统变量你也不知道叫什么名字，加上 VITE 就能避免冲突

### vercel 部署

好像没什么可以说的，就是 environment variables 那里导入一下 env 文件就好了，然后别的就比如 build 命令的，就跟着项目在开发时用的工具一样就行，本地用的 pnpm 就用 pnpm，用 npm 的就用 npm，不要混着用

然后最重要的一点就是，部署之后，我发现电脑能访问前端，但是手机访问不了，过了大概半小时，我才终于发现问题，原来 vercel.app 要翻墙才能访问，还行吧，不强求了，毕竟这可是免费的

## 冷知识

vite 如何识别现在是生产模式还是开发模式，pnpm dev 就是开发模式，生产模式就是 pnpm build，哎这么简单我都没想到

vercel 和 render 一样，会自动关联 github 仓库，每当有 commit 的时候，就会自动重新部署

# 后端开发环境与生产环境区分

今天为了部署环境把项目的配置搞得乱糟糟的，现在我说明一下，怎么区分，大体的思想就是通过环境变量来设置

首先我们安装 python-decouple

```py
pip install python-decouple
```

我给个这个库的使用例子

```py
from decouple import config

ENV = config('ENV', default='dev')
DEBUG = config('DEBUG', default=True, cast=bool)
```

这个 config 就是加强版的 os.getenv，两者一样都能从环境变量当中拿到值，但是 config 还能从.env 文件里面拿到值

然后我具体是这样搞环境区分的

```py
ENV = config("ENV", default="dev")
if ENV == "dev":
    DEBUG = True
    ALLOWED_HOSTS = []
    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.sqlite3",
            "NAME": BASE_DIR / "db.sqlite3",
        }
    }
else:
    DEBUG = False
    ALLOWED_HOSTS = ["hottagplatform.onrender.com"]  # 部署之后的域名
    DATABASES = {
        "default": dj_database_url.config(
            default=os.getenv("DATABASE_URL")
        )  # 这个DATABASE_URL会从环境变量中读取，部署之后会在那边的环境读取
    }
```

试了一下能跑起来，我创建了.env 文件然后什么都没写哈哈

# github actions

这部分我也搞了蛮久的，现在时间不多了，我不知道有没有时间写完，更大的问题是我不知道是不是真的成功了，好吧，我简要的说明

要搞 github actions，去仓库那里，找 actions，然后可以自己创建，我叫 ai 帮我写了一个这个，其实我是在本地创建的，创建在.github/workflows/run_spider.yml 里面，push 上去之后 github 会自动识别

```yml
name: Run Spider

# 触发条件：每天早上6点 + 手动触发
on:
  schedule:
    # 每天北京时间 06:00，注意 GitHub Actions 使用 UTC
    - cron: "0 22 * * *"
  workflow_dispatch: # 允许手动触发

jobs:
  run-spider:
    runs-on: ubuntu-latest

    env:
      ENV: prod
      DJANGO_SETTINGS_MODULE: backend.settings
      SECRET_KEY: ${{ secrets.DJANGO_SECRET_KEY }}
      DATABASE_URL: ${{ secrets.DATABASE_URL }}

    steps:
      # 1️⃣ 检出代码
      - name: Checkout code
        uses: actions/checkout@v3

      # 2️⃣ 设置 Python
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"

      # 3️⃣ 安装依赖
      - name: Install dependencies
        run: |
          pip install -r requirements.txt

      # 4️⃣ 执行数据库迁移
      - name: Apply Django migrations
        run: |
          python manage.py migrate

      # 5️⃣ 运行爬虫脚本并写日志
      - name: Run spider script
        run: |
          echo "Spider run at $(date)" >> spider.log
          python spiders/run_all.py >> spider.log 2>&1
```

看到上面有些被括号括住的变量没有，这些是环境变量，github actions 执行代码的时候，会在一个纯净的 linux 虚拟机里面执行，要设置环境变量的话，要去仓库的 settings 里面找 secrets and variables，然后找到 actions，然后自己创建，我创建了三个环境变量, DATABASE_URL, ENV, SECRET_KEY，和 render 的环境变量基本上是一样的，然后我这里犯了一个错误，我把 DATABASE_URL 设置成 internal url 了，我忘记 github 这里不是 render 了，应该用 external url 的，然后它说是跑成功了，但我看了一下数据库好像时间对不上？待会或者明天再看一下吧，然后我要在部署好的后端那里设置一下 superuser 这样才能访问后台
