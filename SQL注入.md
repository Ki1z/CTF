# SQL注入详解

`更新时间 2025-9-1`

## 概述

SQL注入`SQL Injection`是一种常见的网络攻击手段，攻击者通过在输入字段或请求中注入恶意的 SQL 语句，操控数据库执行意图之外的操作。其目的通常有以下几点

- 窃取敏感数据
- 绕过身份验证
- 修改、删除数据库内容
- 执行系统命令等

### SQL注入的工作原理

1. 输入验证不足

永远不要相信用户的输入，如果Web应用程序没有对用户输入进行验证，攻击者就可以在输入字段中插入SQL代码进行SQL注入攻击

2. 拼接SQL语句

应用程序后端通常将用户输入与SQL查询拼接在一起，形成完整的数据库查询语句，但是由于用户输入验证的不足，这很容易导致攻击者输入SQL代码来截取后端的SQL查询语句

**举例**

```py
import pymysql

conn = pymysql.connect(host='localhost', port=3308, user='root', password='root', database='test')
cursor = conn.cursor()

# python
# 首先接收用户输入
username = input('用户名：')
password = input('密码：')

# 拼接后端查询语句
sql = f"SELECT * FROM users WHERE username='{username}' AND password='{password}'"

# 传入mysql进行查询，并获取查询结果
# 假设已经创建了游标 cursor
cursor.execute(sql)
user = cursor.fetchone()

# 登录验证
if user is not None:
    print("登陆成功！")
else:
    print("登陆失败！")
```

上文是一个简单的后台登录的实现，现在来模拟用户的登录，假设数据中存在用户名为`Ki1z`，密码为`xunlin123`的用户

- 合法输入

输入：`username = Ki1z, password = xunlin123`

后台的SQL语句：`sql = "SELECT * FROM users WHERE username='Ki1z' AND password='xunlin123'"`

数据库返回的数据：`user = (1, 'Ki1z', 'xunlin123')`

- 非法输入（SQL注入）

输入：`username = 1' OR 1=1#, password = 123`

后台的SQL语句：`sql = "SELECT * FROM users WHERE username='1' or 1=1#' AND password='123'"`

这里输入`1'`导致引号提前，`username`字段被用户输入闭合，即本来的用户输入为`'1' or 1=1#'`，但实际为`'1'`，又因为后面拼接的`or 1=1#'`使用井号将原来的后引号注释，SQL不再继续解析，因此后面的`AND password='123'`失效，并且这段SQL语句的语法又是完全正确的，实际传入数据库的语句为`sql = "SELECT * FROM users WHERE username='1' or 1=1"`。因为`or 1=1`的存在，使得`WHERE`永远为真，等价于`SELECT * FROM users`

数据库返回的数据：`user = (1, 'Ki1z', 'xunlin123')`

> <img src="./IMG3/{50F015EB-C4D5-4F0B-8062-01DF0E45B388}.png">
