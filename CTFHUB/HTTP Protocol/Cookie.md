# Cookie

提示`hello guest. only admin can get flag.`

使用`Burpsuite`进行抓包，发现`Cookie: admin=0`

> <img src="./img/6.png">

将其改为`Cookie: admin=1`，发送请求，得到flag

> <img src="./img/7.png">

```flag
ctfhub{bb0f27ec554b09211b5407e2}
```

