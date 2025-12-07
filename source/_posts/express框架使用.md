---
title: express框架使用
date: 2025-10-20 23:30:56
tags: ["express", "node"]
---
# 初始化项目



```(空)
npm init -y
```

init是自动生成package.json之类的文件, 进行初始化

-y是--yes的缩写, 表示跳过配置环节的询问项, 全部使用默认值进行初始化

```
npm install express
```





# 创建入口文件

创建一个app.js, 如下编写

```js
const express = require('express');
const app = express();
const port = 3000;
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});
app.get('/', (req, res) => {
  res.send('Hello, World!');
});
```

使用node app.js命令可以启动后端服务

每次重新编写都需要重新启动服务器, 很麻烦

这里可以安装一个帮助自动重启的库

```(空)
npm install -g nodemon
```

-g是全局安装的意思

```(空)
nodemon app.js
```

启动服务, 每次修改app.js保存的时候, 就会自动重启服务器



# express项目结构

```plaintext
my-express-app/
├── node_modules/               # 依赖包目录
├── public/                     # 静态资源目录
│   ├── css/                    # CSS 文件
│   ├── js/                     # JavaScript 文件
│   └── images/                 # 图片文件
├── src/                        # 源代码目录
│   ├── app.js                  # 应用主入口
│   ├── config/                 # 配置文件
│   │   ├── database.js         # 数据库配置
│   │   └── env.js              # 环境变量配置
│   ├── controllers/            # 控制器层（处理业务逻辑）
│   │   ├── user.controller.js
│   │   └── post.controller.js
│   ├── models/                 # 模型层（数据模型和数据库交互）
│   │   ├── user.model.js
│   │   └── post.model.js
│   ├── routes/                 # 路由层（定义 API 路径）
│   │   ├── user.routes.js
│   │   ├── post.routes.js
│   │   └── index.js            # 路由入口
│   ├── middleware/             # 中间件
│   │   ├── auth.middleware.js  # 身份验证
│   │   └── error.middleware.js # 错误处理
│   ├── services/               # 服务层（复杂业务逻辑）
│   │   ├── email.service.js
│   │   └── upload.service.js
│   └── utils/                  # 工具函数
│       ├── logger.js           # 日志工具
│       └── validator.js        # 验证工具
├── tests/                      # 测试目录
│   ├── unit/                   # 单元测试
│   └── integration/            # 集成测试
├── .env                        # 环境变量配置
├── .gitignore                  # Git 忽略文件
├── package.json                # 项目配置和依赖
├── package-lock.json           # 依赖锁定文件
└── README.md                   # 项目说明文档
```



# 路由操作(接口)

在routes文件夹下创建对应表的route(这里是接口的意思), 命名格式统一为<表名>_route.js吧

为了避免回调地狱, 我们要用同步的方法来编写接口

# restful api 规范

类似这样

| 目的               | 方法      | URI         |
| ------------------ | --------- | ----------- |
| 取得所有使用者資料 | GET       | /users      |
| 新增使用者         | POST      | /users      |
| 取得某位使用者資料 | GET       | /users/{id} |
| 更改某位使用者資料 | PUT/PATCH | /users/{id} |
| 删除某位使用者     | DELETE    | /users/{id} |

# 数据库

### 安装依赖

```bash
npm install sequelize mysql2
```

sequelize库是ORM方式操作数据库的, 就是利用对象来操作, 而不是主要直接运行sql语句

mysql2是sequelize操作MySQL的底层驱动

### 相关配置

数据库的相关配置我们会定义在/config文件夹里面

```js
const { Sequelize } = require('sequelize');
const sequelize = new Sequelize('full_stack_demo', 'Njaso', '114514', {
    host: 'localhost',
    dialect: 'mysql',
    pool: {
        max: 5, // 连接池最大连接数
        min: 0,
        acquire: 30000, // 获取连接的最大等待时间（毫秒）
        idle: 10000 // 空闲连接的最大空闲时间（毫秒）
    }
});

module.exports = sequelize;
```

表定义

定义在/models里面, 命名格式为<表名>_model.js

```js
const sequelize = new Sequelize('database', 'username', 'password', {
  dialect: 'mysql'
});

User.init(attributes, {
  sequelize, // 绑定到这个 Sequelize 实例
  // ...
});
```

```js
User.init(attributes, {
  sequelize,
  modelName: 'User',
  tableName: 'my_custom_users' // 表名强制为 my_custom_users
});
```

modelName是表在数据库当中实际的名字, 而tableName是代码引用的名字

不给TableName的话, 那么代码引用的就是modelName

# 中间件

对请求的参数或者其他东西做统一处理的东西

```js
app.use(express.json());
```

把接口参数转换为json对象

```js
app.use('/table1', table1Route);
```

给table1的接口加上一个/table1的前缀