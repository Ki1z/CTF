# [HCTF 2018]WarmUp 1

- 进入网页，查看源代码，发现source.php

> <img src="../../IMG2/Screenshot 2024-05-15 201105.png">

- 进入source.php，开始代码审计

```php
<?php
    highlight_file(__FILE__);
    class emmm
    {
        public static function checkFile(&$page)
        {   // 创建白名单，白名单内只有source.php和hint.php
            $whitelist = ["source"=>"source.php","hint"=>"hint.php"];
            // 需要参数$page
            if (! isset($page) || !is_string($page)) {
                echo "you can't see it";
                return false;
            }
            // 参数$page的值在白名单内
            if (in_array($page, $whitelist)) {
                return true;
            }
            // 定义变量$_page，赋值为$page
            $_page = mb_substr(
                $page,
                0,
                mb_strpos($page . '?', '?')
            );
            // 参数$_page的值在白名单内
            if (in_array($_page, $whitelist)) {
                return true;
            }
            //以下是将$_page的值进行url解码再次比较
            $_page = urldecode($page);
            $_page = mb_substr(
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }
            echo "you can't see it";
            return false;
        }
    }
    // 需要传入参数file
    if (! empty($_REQUEST['file'])
        && is_string($_REQUEST['file'])
        && emmm::checkFile($_REQUEST['file'])
    ) {
        include $_REQUEST['file'];
        exit;
    } else {
        echo "<br><img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
    }  
?>
```

- 根据代码审计的结果，需要传入一个参数file，需要经过白名单，因此首先需要添加 `hint.php?` ，然后接上需要访问的文件，即hint.php内的ffffllllaaaagggg，所以最后的payload为 `?file=hint.php?/../../../../ffffllllaaaagggg`

> <img src="../../IMG2/Screenshot 2024-05-15 211826.png">