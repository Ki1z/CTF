# [RoarCTF 2019]Easy Java 1

- 进入网页，看似是sql注入，实际上题目已经提示跟java有关， 一般来说，跟java有关的就是WEB-INF泄露，点击help，出现报错，可能存在WEB-INF泄露

> <img src="../../IMG2/Screenshot 2024-06-06 211733.png">

- 使用POST传参 `WEB-INF/web.xml` ，弹出下载框，打开

> <img src="../../IMG2/Screenshot 2024-06-06 212244.png">

- 发现 `com.wm.ctf.FlagController` ，访问 `WEB-INF/classes/com/wm/ctf/FlagController.class`

> <img src="../../IMG2/Screenshot 2024-06-06 212505.png">

- 在文档中发现了一串加密字符 `ZmxhZ3sxYjJlOTQyNS05NThhLTRkN2MtODU5MC0wNWI2OTVlZTc3NDh9Cg==` 使用base64解码得到flag

> <img src="../../IMG2/Screenshot 2024-06-06 212614.png">