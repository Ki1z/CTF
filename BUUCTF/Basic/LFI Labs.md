# LFI Labs

## CMD-1

进入页面，输入1并回车，发现url栏中出现 `?cmd=1` 字样

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/E{M`7LA)P9B8DZWQ%$E~G`O.png?raw=true">

什么也不输入，出现报错，报错内容是system()不能为空

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/HA)%{14$TJF%SO1{TM%D5P4.png?raw=true">

猜测可能是直接将参数cmd的内容带入system()执行，输入ls /查看根目录

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/D_2(K{]4}E`B1FAWMI27X23.png?raw=true">

## CMD-2

页面不显示参数，但是直接输入 `ls /` 有效，页面通过POST传参，也同样使用POST提交表单

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/36CEE3`T$M[VKLS))2DI[KX.png?raw=true">

## CMD-3

直接输入 `ls /` 报错

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/J5{1YIPC99CC{5W{%MDU4R9.png?raw=true">

根据报错信息，可能是将参数domain传入Whois执行，在开头添加一个分号

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/4CR4TY1DV2OQO8C}]YMB6KF.png?raw=true">

## CMD-4

同样使用POST传参，直接输入 `;ls /`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/YYB}@04D9Z2@0HWK@I`0RQO.png?raw=true">

## CMD-5

进入题目发现 `malformed domain name` 域名不符合要求

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/%@VKRBZP0R~(9BQM@%HUTS8.png?raw=true">

输入1并回车，发现url栏出现两个参数，一个domain，一个server

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/8R`H@_3H~T)`H7NMICOA1T5.png?raw=true">

输入google.com，不报错

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/AJLGOC3AJ_RK08TC}E%R~{0.png?raw=true">

猜测可能是对domain进行了过滤，尝试是否对server过滤，将server改为 `ls /`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/MP{DWDA0PLXRZ_~SXK{0)52.png?raw=true">

不报错，说明server参数并未进行过滤，使用管道符，在server后面添加 `|ls /`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/PW%84N8[K5X1BY(_W[0G(U3.png?raw=true">

## CMD-6

使用POST传参，抓包修改即可

## LFI-1

进入页面，直接文件不能为空

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/C$G3@U8(]3CS6]F$FC_E3]0.png?raw=true">

本题未做任何防护措施，打开HDR-1的蓝色背景

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/YG34AKI1~6]$ZG6MQ$]E{GQ.png?raw=true">

## LFI-2

输入1，发现报错

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/S$PXXA])(W6]XF]80F03%I4.png?raw=true">

仔细观察，这里的第一行报错 <b>`Warning`</b>`: include(includes/1.php): failed to open stream: No such file or directory in /var/www/html/LFI-2/index.php on line 11` 中，我们输入的 `1` 变成了 `1.php` ，说明在输入的参数后面还存在一个`.php` ，如果要打开php文件，则直接输入文件名即可，如果要打开其他文件，使用 `%00` 截断即可

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/5COGEHRJQ31[@81PQ4)17ZS.png?raw=true">

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/PW$4YZA76[]}9S]Z_]CJNS1.png?raw=true">

*注：图二中实际已经定位到相应文件，第一句报错消失，但是由于BUUCTF相关配置问题无法打开*

## LFI-3

输入文件地址，发现后缀被过滤

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/H3)J1$GL42FE~EMETV~Z5(Q.png?raw=true">

根据<a href="https://github.com/Ki1z/CTF/blob/main/BUUCTF/Basic/Upload-Labs-Linux.md">Upload-Labs</a>的方法，在后缀后加点、斜杠、空格等无效字符即可，由于BUUCTF使用Linux系统，无法演示效果

## LFI-4

输入1回车，观察报错信息，发现文件前缀被加上了 `class_` ，后缀同样拥有 `.php`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/TB$]I4{5N%5OQ2AY}U6B`@O.png?raw=true">

和LFI-2一样，后缀可以使用%00或者直接省略，前缀使用相对路径，但是这里因为有前缀 `class_` ，因此相对路径前要添加一个左斜杠，即 `/../../HDR-1/blue.php`

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/$7U3{2~RD140E2D0K}JIM]1.png?raw=true">

## LFI-5

观察报错信息没头绪，查看提示，发现是将 `../` 进行过滤

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/%FSF2FYI)B]RWCQZL)LU[{G.png?raw=true">

可以通过套娃的方式（同Upload-Labs），即 `..`<b>`../`</b>`/..`<b>`../`</b>`/HDR-1/blue.php` ，仅加粗部分会被过滤，过滤完后依然是完整的相对路径

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/V~UUD`NXB4[SH6V6VS~UUEO.png?raw=true">

## LFI-6

同LFI-1一样，直接输入 `../HDR-1/blue.php` 即可

## LFI-7

同LFI-2

## LFI-8

同LFI-3

## LFI-9

同LFI-4

## LFI-10

同LFI-5

## LFI-11

无论输入报错信息都不更改

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/R%1J7N7~}(%}`5{FB[2CCAA.png?raw=true">