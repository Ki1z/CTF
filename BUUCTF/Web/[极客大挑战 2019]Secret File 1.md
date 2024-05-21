# [极客大挑战 2019]Secret File 1

- 进入页面，没有有效信息，直接查看原码，发现了Archive_room.php

> <img src="../../IMG2/Screenshot 2024-05-20 185454.png">

- 进入Archive_room.php，有一个secret按钮，点击后跳转到end.php，仔细观察，可以发现是先跳转到action.php，再重定向到end.php，使用burpsuite抓包，发送到然后点击secret按钮，发送到repeater并发送请求，在响应中得到secr3t.php

> <img src="../../IMG2/Screenshot 2024-05-20 185811.png">

- 访问secr3t.php，开始代码审计

```php
<?php
    highlight_file(__FILE__);
    error_reporting(0);
    $file=$_GET['file'];
    if(strstr($file,"../")||stristr($file, "tp")||stristr($file,"input")||stristr($file,"data")){
        echo "Oh no!";
        exit();
    }
    include($file); 
//flag放在了flag.php里
?>
```

- 这串代码很简单，就仅仅是将几个文件包含的关键字进行了过滤，但是文件包含这么多伪协议，随便用一个就行了，故payload如下

`?file=php://filter/convert.base64-encode/resource=flag.php`

- 得到flag.phpd原码的base64，进行解码得到flag

> <img src="../../IMG2/Screenshot 2024-05-20 190626.png">