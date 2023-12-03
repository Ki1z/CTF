# BUU LFI COURSE 1

提供了一个靶机，用浏览器进行连接，发现一串PHP代码

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/WK(IBC}]30{84O`ZBYE)%FV.png?raw=true">

进行分析，此处存在文件包含漏洞， `include` 并未对所包含的文件名进行任何过滤，从而导致攻击者可以任意修改 `$_GET['file']` 的值，随便输入一个文件名试试

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/CJ6RAGD`@QLVLED9JL{FW49.png?raw=true">

报错提示无法打开123456文件，证明刚才的推测正确，下面寻找flag，从当前目录下开始寻找，输入 `file=/flag` ，获取成功

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)Y4CPCB4MM}HK(T9@O`UPNU.png?raw=true">

