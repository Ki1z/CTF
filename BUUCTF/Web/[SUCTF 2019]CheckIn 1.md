# [SUCTF 2019]CheckIn 1

- 进入页面，是一个上传页面，经测试，对文件头进行了检测，图片马中需要包含 `GIF89a`

> <img src="../../IMG2/Screenshot 2024-06-04 195820.png">

- 经测试， `.htaccess` 无法使用，因此需要使用另外一个文件 `.user.ini` ， `.user.ini` 是用户自定义的php配置文件，用户可以通过该配置文件对php进行操作，我们需要上传一个 `.user.ini` 对上传的文件马进行包含，并使用php解析文件

```ini
GIF89a
auto_prepend_file=<Trojan Name>
```

> <img src="../../IMG2/Screenshot 2024-06-04 203927.png">

- 接着访问路径中的index.php文件，出现 `GIF89a` 表示成功

> <img src="../../IMG2/Screenshot 2024-06-04 203927.png">

- 此时index.php中就可以接收一个以REQUEST方式传入的参数admin，参数的值将会被 `eval()` 函数作为php代码执行，使用 `var_dump("Hello, World!");` 进行验证，注意需要分号结束符

`?admin=var_dump("Hello, World!");`

> <img src="../../IMG2/Screenshot 2024-06-04 204552.png">

- 一般来说，flag都会存放在网站根目录的 `/flag` 文件下，所以直接使用 `file_get_contents()` 函数获取flag

`?admin=var_dump(file_get_contents("/flag"));`

> <img src="../../IMG2/Screenshot 2024-06-04 204738.png">