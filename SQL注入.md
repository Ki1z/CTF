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

因此，`UNION`联合注入在一定程度上可以代替`ORDER BY`来判断字段数量，但`UNION`联合注入的功能要更强大，因为`UNION`后的`SELECT`可以查询任意内容

##### 判断回显点

在一般的数据库查询页面中，前端显示的字段数量通常要少于数据表中的实际字段数量，因为需要考虑性能优化、数据安全与隐私保护等。在进行攻击前，就需要攻击者事先判断前端页面中显示的是哪些字段，这个过程被称为判断回显点

假设数据库中有一张数据表

`users`

| id   | username | password |
| ---- | -------- | -------- |
| 1    | admin    | admin    |
| 2    | root     | root     |
| 3    | kiiz     | xunlin   |
| 4    | ocean    | mingxi   |

还有一个用户查询页面，但是为了避免密码泄露，前端只会显示`id`和`username`字段

`injectionPoint.php`

```php
<!doctype html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h2>用户查询</h2>
    <form method="post" action="injectionPoint.php">
        用户名：
        <input type="text" name="username">
        <input type="submit" value="查询">
    </form>
</body>
</html>

<?php


if (isset($_POST['username'])) {
    $username = $_POST['username'];
    $conn = new mysqli("localhost", "root", "root", "test");
    $sql = "SELECT * FROM users WHERE username = '$username'";
    if (!$conn->query($sql)) {
        echo "SQL错误: " . $conn->error;
        exit;
    }
    $result = $conn->query($sql);
    while ($row = mysqli_fetch_assoc($result)) {
        echo "id：".$row['id']."<br>";
        echo "用户：".$row['username']."<br>";
    }
}
```

先进行合法的查询，输入`admin`

> <img src="./img3/{4B9DEC03-AFFC-4F30-8C57-FD4723ECF7B3}.png">

如图所示，后台返回了`admin`的`id`以及`username`字段。

站在攻击者的角度来看，我们很显然不知道这张表到底有几个字段，贸然使用`UNION`很容易导致报错，因此，我们使用`ORDER BY`来判断字段数量，但在此之前，需要先判断漏洞类型，以下是我们尝试后得到的信息

- 输入`admin'`报错`You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''admin''' at line 1`
- 输入`admin'#`不报错，且返回的内容与`admin`一致
- 输入`admin' and 1=1#`不报错，且返回的内容与`admin`一致
- 输入`admin' and 1=2#`不报错，但是没有返回内容

根据上文的描述，这是典型的单引号注入类型，攻击者可以在传入数据库的参数中利用引号闭合后端代码的前引号，以拼接其他SQL攻击语句

然后，再利用`ORDER BY`判断字段数量

- 输入`admin' ORDER BY 4#`报错`Unknown column '4' in 'order clause'`
- 输入`admin' ORDER BY 3#`不报错，且返回的内容与`admin`一致

因此可以确定字段数为3，而前端只显示两个字段，下一步便是判断回显点，判断回显点一般是利用`UNION`来连接一段特定的数据，然后根据前端返回的内容来判断前端显示了哪几个字段

```sql
1' UNION SELECT 1,2,3#
```

例如上面的`payload`，已知数据表中有三个字段，那么我们就在`UNION`后面的`SELECT`中使用`1,2,3`来标记三个字段，如果前端显示了`1,2`，那么就可以使用`1,2`字段的位置显示其他内容

> <img src="./img3/{FE02785C-F106-4A04-8484-8F4C47D60AEF}.png">

**为什么有时候仍然显示合法查询的内容？**

假设数据表中新增了一个用户，用户名为`1`，密码为`1234`

`新增数据`

| id   | username | password |
| ---- | -------- | -------- |
| 5    | 1        | 1234     |

并且前端只显示一条记录

```php
# 只显示一条结果
$row = $result->fetch_assoc();
echo "id: " . $row['id'] . "<br>";
echo "用户: " . $row['username'] . "<br>";
```

此时输入`1' UNION SELECT 1,2,3#`就会发现，前端只显示`id`为5的用户信息，而不是`id = 1`和`username = 2`

> <img src="./img3/{8D75C046-DFBB-4E30-98CA-49A37E5D4EAB}.png">

这主要是查询记录的默认排序导致的，一般来说，`UNION`联合查询的查询内容会排在主查询之后，输入`1' UNION SELECT 1,2,3#`时，主查询能取得一条记录，子查询也能取得一条记录，而恰好前端又只显示一条记录，于是排在第一位的主查询的合法记录就会显示在前端

这种问题的解决方法也很简单，问题的主要来源是主查询与子查询竞争前端显示位，只需要满足主查询无法取得记录即可，即查询数据表中没有的内容

```sql
0' UNION SELECT 1,2,3#
```

> <img src="./img3/{6BC56E43-D589-493B-808B-5C3006303264}.png">

##### 获取敏感信息

判断回显点后，就可以开始逐步获取数据库中的敏感内容，根据SQL注入的一般顺序得知，现在我们需要获取该数据库中的`数据库名`、`数据表名`、`字段名`和`字段值`，按照从大到小的顺序，首先获取数据库名

**DATABASE()**

在MySQL中，存在一个函数`DATABASE()`可以快捷查询当前数据库名

```sql
0' UNION SELECT DATABASE(),2,3#
```

> <img src="./img3/{5A23E0A8-706C-47E7-8773-B6E98BE829F3}.png">

###### information_schema

`information_schema`是MySQL 5.0版本引入的一个信息数据库，`information_schema`库中存储了当前DBMS中所有的数据库的结构内容，包括`数据库信息`、`数据表信息`、`字段信息`、`表索引`、`视图定义`、`约束信息`等

值得注意的是，`information_shcema`数据库中的所有数据表实际上都是视图，不是物理存储的基础表，这意味着它们是在查询时动态生成的，会根据DBMS的变化同步更新

**information_schema.schemata**

`information_schema.schemata`中存储的是当前DBMS中的所有数据库信息，包括

| 字段名                         | 含义                 | 备注                                                       |
| ------------------------------ | -------------------- | ---------------------------------------------------------- |
| **CATALOG_NAME**               | 目录名               | 在MySQL中通常固定为def，表示默认目录                       |
| **SCHEMA_NAME**                | 数据库名             |                                                            |
| **DEFAULT_CHARACTER_SET_NAME** | 数据库的默认字符集   | 指定了该数据库中新建表和字段时默认使用的字符编码           |
| **DEFAULT_COLLATION_NAME**     | 数据库的默认排序规则 | 决定了字符串比较、排序和搜索时的大小写敏感性及字符排序规则 |
| **SQL_PATH**                   | SQL路径              | 保留字段，可能在某些特定环境或未来版本中有用，通常为NULL   |
| **DEFAULT_ENCRYPTION**         | 数据库的默认加密选项 | 指示在该数据库中创建的新表是否默认启用加密                 |

对于攻击者来说，可以利用`information_schema.schemata`的`SCHEMA_NAME`来查询所有的数据库名

**information_schema.tables**

`information_schema.tables`中存储的是当前DBMS中的所有数据表信息，包括

| 字段名              | 含义                       | 备注                                                         |
| ------------------- | -------------------------- | ------------------------------------------------------------ |
| **TABLE_CATALOG**   | 目录名                     | 在MySQL中通常固定为def，表示默认目录                         |
| **TABLE_SCHEMA**    | 数据库名                   | 表示该表所属的数据库                                         |
| **TABLE_NAME**      | 数据表名                   |                                                              |
| **TABLE_TYPE**      | 表类型                     | 一般有普通表`BASE TABLE`与视图`VIEW`                         |
| **ENGINE**          | 表存储引擎                 | 常见的有`InnoDB`、``                                         |
| **VERSION**         | 表结构版本号               | 通常由存储引擎内部使用，表示`.frm`文件的版本，现代存储引擎通常是10 |
| **ROW_FORMAT**      | 行存储格式                 | 通常为`DYNAMIC`，还有`REDUNDANT`、`COMPACT`、`COMPRESSED`等  |
| **TABLE_ROWS**      | 表行数                     | 估算值                                                       |
| **AVG_ROW_LENGTH**  | 平均每行占用的字节数       | 估算值                                                       |
| **DATA_LENGTH**     | 数据部分占用的总字节数     | 不包括索引                                                   |
| **MAX_DATA_LENGTH** | 表允许的最大数据长度       | 通常为0，表示没有长度限制                                    |
| **INDEX_LENGTH**    | 索引部分占用的总字节数     |                                                              |
| **DATA_FREE**       | 已分配但是未使用的存储空间 | 通常表示碎片空间或预留给未来数据增长的空间                   |
| **AUTO_INCREMENT**  | 自动增长的下一个值         | 若没有自动增长，则为NULL                                     |
| **CREATE_TIME**     | 表创建时间                 |                                                              |
| **UPDATE_TIME**     | 表更新时间                 |                                                              |
| **CHECK_TIME**      | 表最后检查时间             | `MyISAM`等引擎会更新此值，`InnoDB`通常为NULL                 |
| **TABLE_COLLATION** | 表默认字符集与排序规则     |                                                              |
| **CHECKSUM**        | 表校验和                   | 如果启用了表校验和，这里会存储校验和值。未启用则为NULL       |
| **CREATE_OPTIONS**  | 表创建时指定的额外选项     |                                                              |
| **TABLE_COMMENT**   | 表注释                     |                                                              |

对于攻击者来说，可以利用`information_schema.tables`的`TABLE_NAME`来查询所有的数据表名

