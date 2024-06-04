# [GXYCTF2019]BabyUpload 1

- 这道题本来挺简单的，但很逆天，进入网页发现上传入口，直接上传PHP文件提示所有ph后缀的文件都不能上传

> <img src="../../IMG2/Screenshot 2024-06-04 193355.png">

- 很容易就能想到上传 `.htaccess` 文件，上传后提示文件类型错误，看来是检测了MIME类型，逆天之处就在于此，这道题的MIME类型不能为PNG或者GIF，只能为JPEG

> <img src="../../IMG2/Screenshot 2024-06-04 193646.png">

- 将MIME类型改为 `image/jpeg` 后上传成功，接下来上传图片马，普通图片马提示还是php类型，看来是对文件内容进行了检测，使用JS语法的图片马上传成功

> <img src="../../IMG2/Screenshot 2024-06-04 193829.png">

- 使用蚁剑连接图片马得到flag

> <img src="../../IMG2/Screenshot 2024-06-04 193906.png">