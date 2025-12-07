---
title: 学习day7
date: 2025-11-17 11:43:53
tags: ["github actions"]
---

# github actions 是否成功运行

今天早上醒来在床上打开了我的网页，看了一下 api 接口，发现数据的日期还是昨天的，我以为是 github actions 没有跑起来，但是现在打开 github 一看，发现是有跑的记录的，所以是我的程序有问题

纠正一下，数据的加入时间就是今天早上的，只因为数据显示的时区不一样，所以才会显示昨天的日期

我问了一下 ai 能不能在模型那里改存储的时间时区，它说建议不要改，展示的时候再改，好吧，那我就不改了，我在 serializer 那里改

```py
from rest_framework import serializers
from .models import HotItem
from django.utils.timezone import localtime


class HotItemSerializer(serializers.ModelSerializer):
    class Meta:
        model = HotItem
        fields = "__all__"

    def to_representation(self, instance):
        data = super().to_representation(instance)
        if instance.crawl_time:
            data["crawl_time"] = localtime(instance.crawl_time).strftime(
                "%Y-%m-%d %H:%M:%S"
            )
        return data

```

覆写 to_representation 就可以了

记得要在 settings 里面修改一个配置

```py
TIME_ZONE = "Asia/Shanghai"
```

一开始我直接加上 TIME_ZONE 发现不行，为什么，因为我没有发现原来默认已经把 TIME_ZONE 写出来了，我以为要自己加上，结果被覆盖成 UTC 了，下次添加配置的时候一定要注意配置是不是已经写出来了

另外介绍一个好用的工具

```py
python manage.py shell
```

这个可以在当前后端环境当中运行 python 脚本，可以用来 debug 和做一些测试

# render 的 free tier 限制

我在文档里面找到说一个月最多只有 750 小时的免费额度使用，我当时心算了一下以为是每天只能用 2.5 小时这样，结果一按计算机是每天 25 小时左右，我寻思不对啊，那其实一直运行着不也可以吗，后来问了一下，发现是一个账号的所有项目的总额是 750 小时，那如果我只有一个项目的话，就可以一直跑了，后来我要搞其他项目的话，其实直接开一个新的账号也行吧，笑嘻了，然后因为 render 服务是十五分钟就会休眠，所以我 github actions 每隔十分钟爬一次就能一直保证后端活着吧，好像不太多，我的 workflow 只执行了爬虫，好像并没有对后端发送请求

# github actions 也是有免费额度的

好像是每个月 2000 分钟，后面搜了一下是私人仓库才有这个限制，公开仓库是没有任何限制的，可以尽情折腾，而且就算有限制，2000 分钟我也是完全用不完，我大概十分钟跑一次 workflow，一个月都只能用掉 100 分钟左右

# github workflow 语法解释

我就解释一下之前 ai 帮我写的 workflow 程序

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

schdule 是定时执行的意思，上面那个 cron 的时间格式分别是分 时 日 月 星期，星号就是所有的意思，这很显然，所以"0 22 \* \* \*" 就是每天 UTC 时间 22 点，也就是北京时间 6 点，因为北京时间是 UTC+8，减去 8 小时就是标准时间

检出代码的意思就是从仓库里面把代码拉到虚拟机里面，然后 python setup 的部分应该也是 github 那边提前写好的一些脚本，我打算把 python 版本改成 3.14，没事找事，因为我发现我本地的版本是 3.14，顺便说一下检查 python 版本的命令是 python -V

上面的 yml 代码会发现有些字段前面带一个-号，这代表它是列表里面的元素，比如说 steps 吧，它就是一个列表，后面就有很多个列表元素，每个-号都代表这是一个新元素，光是理解这个程序应该就够我用了，接下来该了解一下一些 github 的相关插件了，不然每次都要上去 github 那边执行操作还是挺烦的

```yml
schedule:
  # 每天北京时间 06:00，注意 GitHub Actions 使用 UTC
  - cron: "0 * * * *"
  - cron: "10 * * * *"
  - cron: "20 * * * *"
  - cron: "30 * * * *"
  - cron: "40 * * * *"
  - cron: "50 * * * *"
```

每隔十分钟是可以这样写，但是有点笨了

```yml
schedule:
  - cron: "*/10 * * * *"
```

这样写是更加聪明的，它的功能和上面的一样，只是我看不太懂这个语法，不过这样写就对了

# github 相关插件

vscode 的 git 相关操作太简单了，应该不用特意说

原来 github actions 插件一直在左边，悬停一下就能看到显示 github actions 了，然后 git 的插件是一个长得像 Y 型的树

有一个 gitignore 插件，右键文件就可以添加文件到.gitignore 里面了，很方便

# 分支流程

main 分支是实际发布的分支，然后应该再创建一个 develop 分支，开发完成之后再 merge 到 main 分支上面，然后 develop 分支也不用删，相当于是一个版本比较新的 main 分支

# 唤醒后端

```yml
name: Run Spider

# 触发条件：每天早上6点 + 手动触发
on:
  schedule:
    # 每天北京时间 06:00，注意 GitHub Actions 使用 UTC
    - cron: "*/10 * * * *"
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
          python-version: "3.14"

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

      # 6 对后端发送请求，唤醒后端
      - name: Wake up backend
        run: |
          curl -X GET https://your-backend-url.onrender.com/healthcheck || true
```

首先 run: |这里解释一下，这个|是多行字符串的意思，在 run 下面带缩进的行都是属于 run 这个字段的，然后 curl 是系统自带的一个命令，|| true 这里是防止请求失败然后返回错误结果导致 workflow failed 了

# 一个 vue 事件触发很操蛋的地方

比如说我写了一个函数 handleClick，没有参数，那我写@click="handleClick()"就是错的，@click="handleClick"或者@click="() => handleClick()"才是对的，但是如果我这个函数是有参数的，@click="handleClick(e)"这样就是没问题的，虽然按道理来说应该要写成@click="() => handleClick(e)"的，但是这里 vue 提供的一个语法糖，怪不得之前我一直没搞懂

# 在 vscode 上面 merge develop 分支到 main 分支上面

首先切换到 main 分支，然后在 git 插件那里，找 Source Control，在下面的 change 那里找...，然后找到 branch，点击 merge，找到 develop 分支，最后就行了，藏得有点深，我还特意找了个教程

# github 删除文件

可以直接在仓库里面点进文件然后 delete this file

# develop 分支的必要性

说实话个人项目直接在 main 分支上开发就行了，这样效率高多了，前面我开一个 develop 分支只是玩玩而已

# github actions 定时触发频率

我上次设置成十分钟触发一次，结果发现触发频率超低，我干脆改成每分钟触发，结果发现时间间隔在 5 到 20 分钟不等，非常不稳定，哎，免费的东西能用就不错了

# 唤醒后端动作没有用

```yml
# 6 对后端发送请求，唤醒后端
- name: Ping backend
  run: |
    curl -I https://hottagplatform.onrender.com/api/hot/ \
      -H "User-Agent: Mozilla/5.0" \
      --max-time 30 \
      || echo "Ping failed"
```

好像说是一开始单纯的 get 太短太简单了，直接被后端忽略了，所以没有唤醒后端，现在改成这样，加上一个 header，这样后端就不会忽略这个请求了
