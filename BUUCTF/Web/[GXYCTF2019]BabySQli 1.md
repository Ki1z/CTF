# [GXYCTF2019]BabySQli 1

- 尝试万能账号密码失败，最终得到username单引号闭合

> <img src="../../IMG2/Screenshot 2024-06-04 212743.png">

- 因为是POST传参，抓包，发现了一串字符

> <img src="../../IMG2/Screenshot 2024-06-04 212845.png">

- 首先base32，然后base64得到一行代码

> <img src="../../IMG2/Screenshot 2024-06-04 212948.png">

- 看来是后台查询语句

`select * from user where username = '$username'`

- 进行普通注入，发现order被屏蔽，手动尝试得到字段数为3

`name=1' union select 1,2,3#&pw=1`

> <img src="../../IMG2/Screenshot 2024-06-05 201034.png">

- 上图可以看出，回显的结果不是1,2,3，而是仍然将$username的值传入sql然后进行比较，这也就意味着普通的注入是行不通的，猜测后台的代码如下

```php
<?php
$username = POST['name'];
$password = POST['pw'];
$res = mysqli_query($mysql, "select * from user where username = " . $username)
$row = mysqli_fetch_assoc($res)
if ($row['username'] != $username) {
    echo 'wrong user!';
}
else if ($row['password'] != md5($password)) {
    echo 'wrong pass!';
}
```

- 我们在mysql中执行 `select * from user where username = 1 union select 1,2,3` 

> <img src="../../IMG2/Screenshot 2024-06-05 203400.png">

- 从图中可以看出，查询到的username为1，在 `if ($row['username'] != $username)` 为True，因此输出 `wrong user!` ，所以我们尝试 `admin` ，在2处尝试成功

> <img src="../../IMG2/Screenshot 2024-06-05 203548.png">

- 同样，密码按道理来说应该和账号一样，只需要传入的值与union后的值一致即可，但是这道题似乎对密码进行了处理

> <img src="../../IMG2/Screenshot 2024-06-05 203801.png">

- 一般来说，对密码处理最简单也最常用的方式就是使用md5加密，因为我们将union传入的值进行md5加密，最终得到flag

> <img src="../../IMG2/Screenshot 2024-06-05 204101.png">

- 其实也可以使用数组来绕过 `md5()` 函数，这里注入 `md5()` 函数报错后没有返回值，即union后传入的值为 `NULL`

> <img src="../../IMG2/Screenshot 2024-06-05 204234.png">