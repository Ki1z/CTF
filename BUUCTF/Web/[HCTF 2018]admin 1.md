# [HCTF 2018]admin 1

- 进入网页，右上角有登录入口，尝试SQL注入、查看网页源代码、抓包均无效，注册进入页面，查看源代码，发现注释 `<!-- you are not admin -->`

> <img src="../../IMG2/Screenshot 2024-05-21 203617.png">

- 看来是需要尝试登录admin账号，以下有三种方法解决这道题

方法一：爆破

- 已知用户名为admin，对密码进行sniper类型爆破

> <img src="../../IMG2/Screenshot 2024-05-21 204052.png">

- 最后得到密码为123

> <img src="../../IMG2/Screenshot 2024-05-21 205513.png">

方法二：伪造session

- 在更改密码页面的源码中，可以发现它把源码直接给了出来，前往下载源码。源码已经被删除了，这里借用其他人的WP，可以看出，页面对用户信息的检测仅仅是确认用户session中的name的值为admin

> <img src="../../IMG2/Screenshot 2024-05-21 210002.png">

- 那么我们就可以获取我们当前的session，将session中的用户名改为admin，然后再进行加密，传入网页中。那么首先获取当前session

`.eJxFkE-LwjAQxb_KMmcPbbq9CB5cYiULmWJJLZOLaLdq88eFVimN-N03eNnbwHvv92bmCYfz0I1XWN6HR7eAQ_8Dyyd8nGAJqERCxjlU9UyhTqT5sqX6dpIVPRrsS763yDaTbCpbchvItIzY3qPa5bqpDAXByIi83O5SUm2CnDLNqx658xhswEYEqWpGjcyiN8ftZtJeMPQU-5zVvPBlQ1nsj97qWnK8YlQ1X6fIRKbN5VOaOPN6Ba8FtONwPtx_bXf7P4EVXvq4pq9z5JcQIwFD4bQSmQy7Cf2GSV55YoWT4TJLRjOuV2_c7ei7iDieWljAY-yG928gTeD1B1-tZB0.ZkybdQ.EKa2M4VWAA3vwFMCQMirY5ckWOM`

- 使用脚本解密

`{'_fresh': True, '_id': b'524b9e552c5420d92e3ab63b85d7a01dd893b76cef549edcc26b298d5a74467d4b49f7935b3156ac7c294a0fb66f2c9dd1f9f75535da83a7cfd057b7f8820545', 'csrf_token': b'7af2ed6e9483f8371ee273406a604fcae3823f24', 'name': 'abc', 'user_id': '10'}`

- 将session中的name改为admin

`{'_fresh': True, '_id': b'524b9e552c5420d92e3ab63b85d7a01dd893b76cef549edcc26b298d5a74467d4b49f7935b3156ac7c294a0fb66f2c9dd1f9f75535da83a7cfd057b7f8820545', 'csrf_token': b'7af2ed6e9483f8371ee273406a604fcae3823f24', 'name': 'admin', 'user_id': '10'}`

- 对session进行加密，注意session的加密是需要密钥的，在config.py中找到了密钥

> <img src="../../IMG2/Screenshot 2024-05-21 210512.png">

- 使用密钥进行加密

`.eJxFkE-LwjAQxb_KMmcPbbq9CB5cYiULmdKSWiYXcWvV5o8LrVKM-N03eNnbwHvv92bmCfvT2E8XWN7Ge7-A_XCE5RM-fmAJqERCxjlUzYNCk0jzZUv17SQrBjQ4lHxnkW1m2da25DaQ6RixnUdV5bqtDQXByIi83FYpqS5BTpnm9YDceQw2YCuCVA2jVmbRm-N2M2svGHqKfc5qXviypSz2R299KTleMKqar1NkItPm_ClNnHmzgtcCumk87W-_tr_-n8AKL31c0zc58nOIkYChcFqJTIZqRr9hkteeWOFkOD8koweuV2_c9eD7iDgc_XCFBdynfnx_B9IEXn8uvWUA.ZkycnQ.hC8fcUzEPT2s-o5ZpL2W-uJ59RM`

- 将伪造的session传入网页中刷新，得到flag

> <img src="../../IMG2/Screenshot 2024-05-21 210812.png">

方法三：Unicode欺骗

> <img src="../../IMG2/Screenshot 2024-05-21 211013.png">

- 在routes.py中，修改密码的有这样一行代码 `name = strlower(session['name'])` ，PHP本身并没有 `strlower()` 函数，在下方发现了自定义函数，内部使用了一个方法 `nodeprep.prepare()` ，该方法可以返回字符串的小写形式，但是该方法有个特性，在参数为Modifier Letter Capital修饰符大写字母时，会返回其大写字母，如下

> <img src="../../IMG2/Screenshot 2024-05-21 211433.png">

- 因此可以利用这个特性，在注册用户名时使用修饰符大写字母ᴬᴰᴹᴵᴺ，然后在通过两次该方法得到admin。同时，在登录时也发现了 `strlower()` 函数，也就意味着仅需要登录后更改一次密码，就可以使用更改的密码登录admin账号获取flag

> <img src="../../IMG2/Screenshot 2024-05-21 211701.png">

- 注册并登录，可以看到用户名已经变更为了ADMIN

> <img src="../../IMG2/Screenshot 2024-05-21 212221.png">

- 更改密码为abc，使用密码登录admin拿到flag