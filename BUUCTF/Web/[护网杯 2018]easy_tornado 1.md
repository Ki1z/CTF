# [护网杯 2018]easy_tornado 1

- 进入页面，有三个文件，打开 `/flag.txt` 提示flag在 `/fllllllllllllag`

> <img src="../../IMG2/Screenshot 2024-06-02 192829.png">

- 第二个文件 `welcome.txt` 没有有效信息，第三个文件 `hints.txt` 提示 `md5(cookie_secret+md5(filename))`

> <img src="../../IMG2/Screenshot 2024-06-02 192954.png">

- 观察url，发现文件是通过 `/file?filename=/{filename}&filehash={filehash}` 访问，也就是说 `hints.txt` 里提示的就是filehash的算法

> <img src="../../IMG2/Screenshot 2024-06-02 193349.png">

- 现在需要做的就是找到cookie_secret，直接抓包没有cookie信息，回想题目名叫 `easy_tornado` ，可能需要进行模板注入，将filename的值改为1，出现 `error?msg=Error`

> <img src="../../IMG2/Screenshot 2024-06-02 193546.png">

将msg的值改为 `{{1*1}}` ，回显 `ORZ` ，存在模板注入

> <img src="../../IMG2/Screenshot 2024-06-02 193945.png">

- 因为题目是tornado模板，在tornado中，存在对象 `handler.settings` 存储环境配置信息，因此将msg的值改为 `{{handler.settings}}` 得到cookie_secret

> <img src="../../IMG2/Screenshot 2024-06-02 194156.png">

- 计算md5值，得到payload为 `?filename=/fllllllllllllag&filehash=49319e0b9b380a1c690fa93e2ff2cb7e` ，最后得到flag

> <img src="../../IMG2/Screenshot 2024-06-02 194717.png">

*注：filename包含文件名前面的 `/` ，即 `/fllllllllllllag`*