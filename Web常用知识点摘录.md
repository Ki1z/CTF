# Web常用知识点摘录

`更新时间 2024-5-19`

---

# is_numeric()

is_numeric(arg)函数可以检查参数arg是否为一个数字或数字字符串

### 绕过

is_numeric()对于任何以 `0x` 开头的数字都返回true

在任何数字前加上 `%00` 都会使is_numeric()返回false

# 弱比较

弱比较是指PHP中的 `==` 比较，该运算符只会比较两端数据的值大小，不会比较数据类型

### 绕过

在字符串进行弱比较时，字符串会强制转换为int类型，若数字后面存在字符串，则字符串直接省略

对于任何 `0e` 开头的科学计数法数字，都会被转换为0

# ereg()

ereg(arg1, arg2)函数可以在arg2中按照arg1模式搜索指定的字符串，匹配成功返回true，失败返回false

### 绕过

ereg()函数会忽略截断符 `%00` 后面的内容

# strpos()

strpos(arg1, arg2)函数可以查找在arg2中arg1第一次出现的地点，若查询失败则返回false，strpos()不会识别 `%00` 截断符

# intval()

intval(arg)可以返回arg的整数值

### 绕过

当某个特定的数字被过滤时，可以使用该数字的8/16进制形式

字符串 `'0x1'` 和 `'0b1'` 都会被转换为0，但是在进行弱比较时 `'0x1'` 等于1， `'0b1'` 等于0

# SQL约束攻击

Sql约束攻击指的是在Sql数据库中，数据库对于输入的数据没有长度限制，而数据表本身对长度有限制，从而导致输入的数据在经过修剪后覆盖原本数据的攻击

# trim()

trim(arg1, arg2)函数可以在arg1字符串的两端清除arg2

### 绕过

在字符串前添加 `%0c` 可以使trim()无效化

# addslashes()

addslashes(arg)函数可以在arg字符串的双引号前面添加反斜杠

# 回文 palindrome

回文是指正反读都一样的字符串

# strval()

strval(arg)函数可以返回arg的字符串值

# strrev()

strrev(arg)函数可以反转arg字符串

# strlen()

strlen(arg)可以返回arg的字符串长度

### 绕过

在Linux中，需要在多行写一条命令，则可以在行末使用 `\` 来作为标记，直到没有 `\` 的一行作为该命令的结尾

一般来说，都会使用 `ls -t` 来构建一句话木马，首先反序列创建相应文件，然后输入 `ls -t>0` 来创建生成木马的文件，然后输入 `sh 0` 来生成木马，接着用蚁剑连接就行了

可以使用数组绕过

# 命令分隔符

常见的命令分隔符有 `;` , `|` , `&` , `||` , `&&` ，如果他们被屏蔽了，还可以使用 `%0a` 或 `%0d`

# preg_match()

preg_match(arg1, arg2)函数在arg2中查找arg1的内容，arg1是正则表达式

# ord()

ord(arg)函数可以将arg字符串中的首字符转换为ascii码，在PHP中常用来限制特定字符

### 绕过

对于限制字符为数字的，可以使用16进制数字

# md5()

md5(arg)函数可以返回参数arg的md5值

### 绕过

在进行弱比较时，为md5()函数内传入数组会报错，但是返回True

# SQL万能账号密码

## 账号

- 数值型万能账号

```
a or true #

a or 1 #

a or 1=1 #

a or true -- a

a or 1 -- a

a or 1=1 -- a
```

- 单引号字符型万能密码

```
a' or true #

a' or 1 #

a' or 1=1 #

a' or true --a

a' or 1 -- a

a' or 1=1 -- a
```

- 双引号字符型万能密码

```
a" or true #

a" or 1 #

a" or 1=1 #

a" or true -- a

a" or 1 -- a

a" or 1=1 -- a
```

## 密码

- 数值型万能密码

```
admin #

admin -- a
```

- 单引号字符串型万能密码

```
admin' #

admin' -- a
```

- 双引号字符串型万能密码

```
admin” #

admin" -- a
```

# mb_substr()

mb_substr(arg1, arg2, arg3)函数从arg1中提取字符，从arg2开始，arg3是要提取的长度

# mb_strpos()

mb_strpos(arg1, arg2, arg3)函数返回arg2在arg1中出现的位置，arg3是开始的位置

# 伪协议

在php中，内置了很多URL风格的封装协议，可用于包含指定文件

### file://

file://用于访问本地文件系统，后常接文件绝对路径

`file=file://C:/abc/abc.php`

### php://

php://用户访问输入输出流，在CTF中常用php://filter来将包含文件作为base64编码输出，如果直接作为php代码输出则无法查看源代码

`file=php://filter/convert.base64-encode/resource=abc.php`

### zip://

zip://用于访问压缩包文件

`file=zip://C:/abc/abc.zip%23abc.php`

*注：%23后面的abc.php是压缩包内的文件*

### data://

data://与php://类似，但是data://可以直接执行php代码

`data://text/plain,<?php phpinfo();?>`

同时也可以使用base64编码

`data://text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=`

# Linux空格替换

- `${IFS}`

- `$IFS$1`

- `${IFS`

- `%20`

- `%09`

# 堆叠注入

堆叠注入是SQL注入中的一类，一般不会遇到，堆叠注入的原理是使用分号分隔多个sql语句，让后台函数同时执行。但是绝大部分php均使用 `mysqli_query()` 函数，该函数只会执行一个语句

# SQL预编译命令

在SQL中，可以使用prepare关键字预先定义好需要执行的命令，然后使用execute执行

`prepare <name> from '<command>'`

`execute <name>`

\<command>可以为SQL中的函数，如 `concat()` 等

\<command>内容可以为16进制，但是需要添加 `0x`

# handler

handler是Mysql独有的查询字段数据的命令，分为打开，读取和关闭三步

`handler <table> open;`

`handler <table> read;`

`handler <table> close;`