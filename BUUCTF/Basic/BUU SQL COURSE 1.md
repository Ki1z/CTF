# BUU SQL COURSE 1

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

