# [SUCTF 2019]EasySQL 1

- 进入网页，有一个输入框，提示是Give me your flag, I will tell you if the flag is right，输入1，返回了一个数组

> <img src="../../IMG2/Screenshot 2024-05-17 221333.png">

- 再输入一个a，返回空白

> <img src="../../IMG2/Screenshot 2024-05-17 221605.png">

- 经过测试，sleep、from、union等关键词均被过滤了，那么正常的注入肯定不行，尝试堆叠注入，发现成功

> <img src="../../IMG2/Screenshot 2024-05-17 223619.png">

- 查询表成功，但是查询字段因为要指定数据表，查询一定会失败，这道题似乎陷入了僵局。仔细思考，传入非零数字均会返回1，字母均不会返回任何值，是否有可能后台使用了某个逻辑运算符，可能是逻辑与拼接True值或者逻辑或拼接False值，这不重要，重要的是我们可以利用逗号，正常直接输入通配符 `*` 在进行逻辑比较时会报错，但是如果使用逗号拼接一个1，那么就能让 `*` 永远成立

- 下面我们来进行一个测试，假设后台代码是 `select $_POST['cmd'] || 0 from flag` ，我们传入1，则返回1，传入a，则返回0

> <img src="../../IMG2/Screenshot 2024-05-19 183342.png">

- 现在我们传入 `*,1` ，那么查询语句就变成了 `select *,1 || 0 from flag` ，此时1先与0进行逻辑运算，然后再作为新字段与flag中的内容拼接在一起

> <img src="../../IMG2/Screenshot 2024-05-19 183657.png">

- 故payload为 `*,1`，最终得到flag

> <img src="../../IMG2/Screenshot 2024-05-17 235911.png">