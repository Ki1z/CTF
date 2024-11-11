# vim缓存

vim缓存的后缀名一般为`.swp`、`.swo`、`.swn`，而且一般的缓存文件都是隐藏的，即文件名`.`开头

根据提示直接访问`.index.php.swp`，然后扔进kali，输入`vim -r index.php.swp`找到flag

> <img src="./img/6.png">

```flag
ctfhub{f6c70fd76ccb0ec0b042fe26}
```

