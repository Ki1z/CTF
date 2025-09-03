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

3. 执行恶意SQL

如果应用程序没有对输入进行适当的清理或转义，恶意SQL代码将被数据库服务器直接执行，以此造成的后果通常是难以预料的

4. 数据泄露或破坏

攻击者可以利用SQL注入来查询、修改或删除数据库中的数据，或者执行数据库管理系统的系统命令，甚至通过权限修改以达成完全控制数据库服务器的目的

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

### SQL注入的危害

- 数据泄露：攻击者获取数据库中的用户名、密码、银行卡号等敏感信息
- 权限提升：攻击者可能通过注入命令获得更高的访问权限
- 数据篡改：数据库内容被修改或删除
- 服务中断：恶意 SQL 代码可能导致数据库崩溃，影响系统可用性
- 执行系统命令：通过数据库扩展功能，攻击者可能直接操作操作系统

## SQL注入的基本漏洞类型

由于后台数据库查询语句拼接的格式不同和业务需求不同，SQL注入一般分为了三种基本类型

### 数字型注入

一般出现于通过代号、数字id等方式进行查询时，可能的SQL语句如下

```py
# python

# 获取查询id
id = input()

# 拼接sql语句
sql = f'SELECT * FROM users WHERE id={id}'
```

**如何判断**

- 查询处出现`代号`、`序号`、`id`等字样
- 尝试`1'`、`1"`、`1'#`、`1"#`均报错
- 尝试`1 AND 1=1`查询结果与`1`相同
- 尝试`1 AND 1=2`不报错，但是没有结果

### 字符型注入

绝大部分数据库查询的参数均是字符串类型，在数据库中，字符串类型的数据需要用引号括起来，而SQL支持单引号和双引号两种方式，因此字符型注入又分为单引号注入和双引号注入，两者的原理一致。可能的SQL语句如下

```py
# python

# 获取用户名
uname = input()

# 拼接sql语句
sql = f"SELECT * FROM users WHERE username='{uname}'" # 单引号类型
sql = f'SELECT * FROM users WHERE username="{uname}"' # 双引号类型
```

**如何判断**

- 尝试`1'`或`1"`报错
- 尝试`1'#`或`1"#`查询结果与`1`相同
- 尝试`1' AND 1=1#`或`1" AND 1=1#`查询结果与`1`相同
- 尝试`1' AND 1=2#`或`1" AND 1=2#`不报错，但是没有结果

**为什么要判断SQL注入的漏洞类型**

了解SQL注入的类型是SQL注入的基础和前提，只有准确掌握后台SQL语句的拼接结构，才能利用漏洞进行有效的攻击

## SQL注入攻击

### SQL注入攻击的一般顺序

以一般CTF比赛为例，我们对一道SQL注入类题目进行攻击

1. 信息收集，寻找题目中存在SQL查询的位置与可能的SQL注入点
2. 漏洞验证，使用上方的判断方法验证SQL注入点的存在以及判断SQL注入点的漏洞类型

3. 提取信息，利用拼接的SQL语句查询数据库中的敏感内容，包括`库名`、`表名`、`字段名`和`字段值`

4. 寻找flag，在已知的信息中寻找flag可能出现的位置，并最终找到该题的flag

### SQL注入攻击的攻击类型

#### ORDER BY 攻击

`ORDER BY`攻击是指利用数据库中的`ORDER BY`排序语句来泄露当前数据表中字段数量的攻击手法

**攻击原理**

在数据库中使用`ORDER BY`进行排序时，排序的参数可以为字段名或字段序号，如以下数据表

`users`

| id   | username | password |
| ---- | -------- | -------- |
| 1    | admin    | admin    |
| 2    | root     | root     |
| 3    | kiiz     | xunlin   |
| 4    | ocean    | mingxi   |

查询语句

```sql
SELECT *
FROM users
ORDER BY username;
```

完全等价于

```sql
SELECT *
FROM users
ORDER BY 2;
```

> <img src="./img3/{328D5E67-0E64-41B9-BB64-006A0F899F80}.png">

当`ORDER BY`后的参数为字段序号时，序号不能超过数据表中的字段数量，如当前表中存在3个字段，使用`ORDER BY 4`则会报错

> <img src="./img3/{11F148B9-F120-4CA3-A9A0-C4D7A1DE368D}.png">

攻击者恰好可以利用这个报错来泄露当前数据表中的字段数量

*注：`ORDER BY`攻击不是必须的，字段数量也可以通过其他攻击手法获取*

#### UNION 联合注入

`UNION`联合注入是指使用SQL语句中的`UNION`关键字来达到执行任意查询的攻击手法

**攻击原理**

`UNION`是SQL查询两张不同的表时使用的关键字之一，如以下数据表，我们希望其底部额外添加一条查询内容

`users`

| id   | username | password |
| ---- | -------- | -------- |
| 1    | admin    | admin    |
| 2    | root     | root     |
| 3    | kiiz     | xunlin   |
| 4    | ocean    | mingxi   |

`额外内容`

| id   | username | password |
| ---- | -------- | -------- |
| 5    | 6        | 7        |

查询语句

```sql
SELECT *
FROM users
UNION
SELECT 5,6,7;
```

> <img src="./img3/{6DA83C8B-0957-4990-B1FF-F10881150106}.png">

在进行`UNION`联合查询时，必须保证前后两张表的字段数量完全一致，否则会报错

> <img src="./img3/{045015D4-E6D9-4E0B-86C3-5EA76DA452D0}.png">

因此，`UNION`联合注入在一定程度上可以代替`ORDER BY`来判断字段数量，但`UNION`联合注入的功能要更强大

##### 判断回显点

