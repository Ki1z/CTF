# 云演Web题库

`更新时间 2024-4-16`

---

# 矛盾

`https://www.yunyansec.com/#/experiment/ctfdetail/5`

考点：PHP代码审计，is_numeric()，弱比较

- 进入网页，直接看到PHP代码

> <img src="./IMG/Screenshot 2024-04-16 183916.png">

- 进行代码审计

```php
<?php
//以GET方式获取一个参数num，赋值为变量num
$num=$_GET['num'];
//如果num不是一个数字或数字字符串，进入if
if(!is_numeric($num))
{
echo $num;
//如果num等于1，进入if
if($num==1)
//得到flag
echo 'flag{**********}';
}
?>
```

- 构建payload

根据审计结果，我们需要通过GET方式传入一个参数num，这里使用url传参。然后是num的取值，既要num等于1，又要求num不能为数字，可以利用PHP弱比较的特性，当字符串与数字进行比较时，字符串会强制转换为int类型，如果数字在字符前面，则数字后面的字符全部清空，因此payload为 `?num=1a`

> <img src="./IMG/Screenshot 2024-04-16 192850.png">

# 单身一百年也没用

`https://www.yunyansec.com/#/experiment/ctfdetail/6`

考点：解码，base64

- 进入网页，有一个超链接，点击后无任何有效信息，但是仔细观察，发现存在中间页面

> <img src="./IMG/Screenshot 2024-04-16 194048.png">

- 打开BurpSuite，抓包，查看请求头，没有任何信息，发送到Repeater中，在响应头中发现了Key

> <img src="./IMG/Screenshot 2024-04-16 200630.png">

- 一眼看出是base64编码，解码得到flag

> <img src="./IMG/Screenshot 2024-04-16 200730.png">

# 