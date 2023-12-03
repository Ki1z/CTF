# BUU BURP COURSE 1

打开页面，提示只能本地访问

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/UU[QZC(PM1B$]M`2ZZGON0Q.png?raw=true">

修改请求头伪造本地连接，下面有几种方法

**1. 使用相关插件**

这里我使用ModHeader

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)@~HSB`3L1%6ZFUUZ`]KU7B.png?raw=true">

在ModHeader中添加几行Request Hearder

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/1.png?raw=true">

**2. 使用BurpSuite**

打开BurpSuite进行抓包，并发送到Repeater中

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/9T~WF7T[TL@TXA(}SZN$XAG.png?raw=true">

添加上文中的请求头

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/WM9%7`DLE2V)P%R1VR1{JRA.png?raw=true">

进入网页后，点击登录，获取flag