# [GXYCTF2019]BabySQli 1

- 尝试万能账号密码失败，最终得到username单引号闭合

> <img src="../../IMG2/Screenshot 2024-06-04 212743.png">

- 因为是POST传参，抓包，发现了一串字符

> <img src="../../IMG2/Screenshot 2024-06-04 212845.png">

- 首先base32，然后base64得到一行代码

> <img src="../../IMG2/Screenshot 2024-06-04 212948.png">

- 看来是后台查询语句