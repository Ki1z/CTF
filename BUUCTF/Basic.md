# Basic

`更新时间：2023-11-22`

注释解释：

- `<>`必填项，必须在当前位置填写相应数据

- `{}`必选项，必须在当前位置选择一个给出的选项

- `[]`可选项，可以选择填写或忽略

## Linux Labs

提供了一个靶机

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/3JX[{R3{T(P9@O)D4}W~_YY.png?raw=true">

使用浏览器进行连接，没有有效信息

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/I]0A1`2BXQ`(E_0UT8F9]W1.png?raw=true">

根据第一步的提示，使用ssh

**基本语法**

```shell
ssh -p <port> <username>@<hostname>
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/MBI~ON)M1A{A0FUG%TR@ZMT.png?raw=true">

连接成功，使用 `cd /` 进入目录，再使用 `ls` 查看当前目录下所有文件\文件夹，发现flag.txt

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/1HD170ZGQ9T037)70]I{V14.png?raw=true">

使用 `vim <filename>` 打开flag.txt，获取flag成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/HHIVUJ)GI9(ZDX~BS~Q~P$8.png?raw=true">

## BUU LFI COURSE 1

提供了一个靶机，用浏览器进行连接，发现一串PHP代码

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/WK(IBC}]30{84O`ZBYE)%FV.png?raw=true">

进行分析，此处存在文件包含漏洞， `include` 并未对所包含的文件名进行任何过滤，从而导致攻击者可以任意修改 `$_GET['file']` 的值，随便输入一个文件名试试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/CJ6RAGD`@QLVLED9JL{FW49.png?raw=true">

报错提示无法打开123456文件，证明刚才的推测正确，下面寻找flag，从当前目录下开始寻找，输入 `file=/flag` ，获取成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)Y4CPCB4MM}HK(T9@O`UPNU.png?raw=true">

## BUU BRUTE 1

老样子，用浏览器连接

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/MADV_%2{[(811H6JBDSIR9M.png?raw=true">

试试一些弱密码

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/ZT_[D)VS7$D]FJ7}{U%WR81.png?raw=true">

我这里用户名和密码都用的是admin，提示密码只有4位数字，换一个用户名试试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/D}[]PA`HRM0ESS77N])JE~2.png?raw=true">

用户名确认是admin，使用BurpSuite暴力破解

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/O1`7GR}AXCUG[O~D0A2MR2C.png?raw=true">

提示需要4位数字，可以直接将Payload集设置为Brute forcer

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)MFES2%@DA@P`@8SZ0P(A%K.png?raw=true">

在经历了20分钟的等待后，终于得到flag

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/G[OALFQA`0$2U_W$[M}Z6@8.png?raw=true">

## BUU SQL COURSE 1

进入网站，是一个新闻页

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/B809X%YU~E4XK]T[QH8$5PQ.png?raw=true">

点击链接没有任何异常，对其进行抓包，发现是通过 `GET` 获取 `/backend/content_detail.php?id=1` 下的值

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/JGB]Y3N~AK]5SB1KZDGBWYN.png?raw=true">

打开链接，尝试更改其他id，内容改变，确定id处存在SQL注入漏洞

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/%~}8E2FGNBMQ]Q8KNW[@}R1.png?raw=true">

判断注入点，插入 `?id=2 and 1 = 1` 查询成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/WFQ}(96EW4UW%%AB~8A2RMY.png?raw=true">

插入 `?id=2 and 1 = 2` 结果为空

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/G61F%[WAMX6$NGZTU(5FN)D.png?raw=true">

插入 `?id=2'` 结果为空

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/%F_G[6@BIZ3EQG_0@BJQ5}0.png?raw=true">

确认为数字型漏洞，下面使用 `order by` 判断字段数，字段数为2

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/6R5D112RP184UF}2$F`ES%G.png?raw=true">

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/S8S%[O`1NMDS~TH4{RH4KT5.png?raw=true">

判断回显点

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/U(@C7CY2PJ{O_V@O486D@NL.png?raw=true">

页面不更改，将id设置为0

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/RQ5IDP)M((QPE@B$%@9GT6B.png?raw=true">

下面过程省略，最后找到管理员账号和密码

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/X}63AY{0F6V[)A$8E)6C((I.png?raw=true">

*注：这里的密码并不是md5码*

用用户名和密码进行登录，得到flag

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/UUTNHJW2G46LF_0C)]K5ES9.png?raw=true">