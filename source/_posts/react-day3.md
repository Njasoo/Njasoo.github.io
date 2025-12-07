---
title: react-day3
date: 2025-11-07 10:20:29
tags: ["react"]
---

# 后端配置 cors

今天要来做一个前后端分离的小项目，后端用的是 django，直接一步到位在后端解决跨域请求，首先安装 cors

```bash
pip install django-cors-headers
```

然后在 settings.py 里面找到 MIDDLEWARE 添加以下设置

```py
MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.security.SecurityMiddleware',
    ...
]
```

然后还要添加上一个配置

```py
CORS_ALLOW_ALL_ORIGINS = True
```

# 配置 JWT

虽然之前已经搞过几次了，但是现在还是复习一下，首先是安装

```bash
pip install djangorestframework-simplejwt
```

然后在 settings.py 里面添加以下配置

```py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    )
}
```

# django 自带的 user 模型的在创建对象的时候是自动加密密码的

就是 User.objects.create_user 这个方法，就不需要自动手写加密了

# 记得要在 installed_apps 那里加上 rest_framework

忘记加上这个配置的话，访问后端的时候是不能看到自动生成的接口界面的

# 复习一下 ModelViewSet 和其他独立功能的 view

ModelViewSet 是自动生成增删改查的功能，如果只需要其中几个功能的话，分别是 list / retrieve / create / update / destroy，下面是一段示例代码

```py
from rest_framework.generics import CreateAPIView
from .models import User
from .serializers import UserSerializer

class RegisterView(CreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

这里说一下 list 和 retrieve 的区别，list 是获取全部对象，retrieve 是获取单个对象，list 对应的 url 就是/api/users/，retrieve 对应的就是/api/users/\<id\>/

# ModelViewSet 和 APIView 的路由写法

ModelViewSet 可以直接用 drf 的 router 写，而 APIView 要自己指定一下路径

这个是 drf 的 router 写法，通常这个 register 的路径都会规定在前面加上 r，其实好像不加也行，但是总之这样写比较不会出问题，我还是问一下 ai 吧，这个 r 是用来让反斜杠不再被当成转义字符解释，这样可以在路由里面写一些正则表达式的路径

```py
from rest_framework.routers import DefaultRouter
from .views import TodoViewSet

router = DefaultRouter()
router.register(r'todos', TodoViewSet)

urlpatterns = router.urls
```

这个是 APIView 的路由写法

```py
from django.urls import path
from .views import RegisterView
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path("register/", RegisterView.as_view()),
    path("login/", TokenObtainPairView.as_view()),
    path("refresh/", TokenRefreshView.as_view()),
]
```

# 格式化代码配置
我之前在vscode的settings那里只能配置一个默认的formatter，原来可以在settings.json里面单独为某个语言配置，ctrl+shift+p然后打开open user settings，加上以下配置
```json
{
    ...,
    "editor.formatOnSave": true,
    "editor.fontSize": 16,
    "[javascriptreact]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescriptreact]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[html]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[css]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[python]": {
        "editor.defaultFormatter": "ms-python.black-formatter"
    },
}

```
记得要安装prettier和black formatter这两个插件

# vite创建命令
```bash
npm create vite@latest client
```
还能直接这样创建，然后进去的时候再选对应的前端框架