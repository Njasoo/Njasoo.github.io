---
title: php快速入门
date: 2025-10-20 23:33:36
tags: php
---
# .php文件可以当成html文件来写

php语句只在php文件当中生效, 或者在html文件当中引入php也可以生效

# 查看数据类型和值

var_dump(x)

# 字符串连接符

.

# EOF定界符

<<<EOF

必须顶头写, 独占一行, 结束标记要有分号

```php
<?php
echo <<<EOF

EOF;
```

其实结束字符可以用任意的字符替代

使用了定界符, echo的中间语句如果有变量的话, 可以直接写进去, 而不需要用.来连接

```php
echo <<<EOF
 $a $b $c
EOF;
```

也可以直接写单双引号, 不需要转义符号

可以解析html语句

# php标签

<?php

# 网站在php_study里面注册

里面可以设置首页, 打开项目根目录

开启网站的时候记得打开apache和mysql等服务

# foreach遍历

```php
foreach ($maoshu as $value) {
    ...
}

foreach ($maoshu as $key => $value) {
    ...
}
```

# 内建函数

count(): 计算数组的长度

date函数

```php
var_dump(date('Y-m-d H:i:s'));
```

time(): 获取当前时间

# 函数的定义

```php
<?php
function test($a) {
    echo $a;
}
test("test");
?>
```

指定类型

```php
<?php
function add(int $a, int $b) {
    echo $a + $b;
}
add(1, 2);
?>
```

严格限制类型

declare(strict_type=1);

# 类型比较

==只判断是否相等

===判断值和类型是否都相等

# 数组转JSON格式

print_r好像只能打印json格式的东西

只要是json格式的它都能打印成人类比较能看懂的东西

如果我们发送了一个数组,单纯的用json()来转换是不能够转换成json格式的

这样才可以

```php
print_r(json_encode($_POST,JSON_UNESCAPED_UNICODE));
```

# 字符串判空empty()

empty($str);



# print_r()

这个函数是在控制台打印变量的

# require()

```php
$config=require __DIR__."./../config/database.php";
```

# 数据库连接 

PDO的连接方式,还有一种是mysql的连接方式

```php
public static function connect(){
            $config=require __DIR__."./../config/database.php";
            $dbms=$config["dbms"];
            $host=$config["host"];
            $user=$config["user"];
            $pass=$config["pass"];
            $dbName=$config["dbName"];

            $dsn="$dbms:host=$host;dbName=$dbName";

            try{
                $conn=new PDO($dsn,$user,$pass);//初始化一个PDO对象
                echo "连接成功<br/>";

                //关闭连接
                $conn=null;
            }catch(PDOException $e){
                die("错误!: ".$e->getMessage()."<br/>");
            }
        }
```

mysqli的连接方式

```php
$conn=new mysqli($serverName,$username,$password);//初始化mysqli对象
```

### 更加一步到位,直接连接到数据库

```php
$conn=new mysqli($serverName,$username,$password,$dbName);
```



# 函数参数引用传递

加上&符号就行了

```php
function func(&$x)
```

# json()格式化返回数据

```php
json_encode($res)
```

# 获取数据库查询结果

```php
$res=$conn->query($statement);
if($res){
    while($row=$res->fetch_assoc()){
        ...
    }
}
```

# 终止php程序

```php
die(message);
```

# 解决中文显示问题

```php
echo json_encode($rest,JSON_UNESCAPED_UNICODE);
```

