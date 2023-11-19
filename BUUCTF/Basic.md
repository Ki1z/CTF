# Basic

`更新时间：2023-11-19`

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