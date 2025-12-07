---
title: 基于django-channels的多人聊天室demo
date: 2025-10-20 23:52:08
tags: django python redis channels
---
# django项目创建
### 创建虚拟环境变量

```bash
$ python -m venv env
```

这个命令在根目录底下创建一个叫env的文件夹作为一个项目的虚拟环境，与conda创建的虚拟环境不同的是，venv的虚拟环境是以项目为单位的，而conda是以任务的类别为单位的，所以如果是web开发的话，上面这种创建方式可能会比较好一点



### 创建项目文件夹

```bash
django-admin startproject $project_name
```

如果要防止文件夹目录嵌套的话，使用下面的命令，这样就不会在外面多套一层文件夹

```bash
django-admin startproject $project_name .
```

但是如果我是做前后端分离的项目的话，我还是会选择第一个命令



