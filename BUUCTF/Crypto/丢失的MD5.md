# 丢失的MD5

- 下载压缩包，里面是一个python文件

```py
import hashlib   
for i in range(32,127):
    for j in range(32,127):
        for k in range(32,127):
            m=hashlib.md5()
            m.update('TASC'+chr(i)+'O3RJMV'+chr(j)+'WDJKX'+chr(k)+'ZM')
            des=m.hexdigest()
            if 'e9032' in des and 'da' in des and '911513' in des:
                print des
```

- VScode提示语法错误，将print des改为print(des)后运行，出现错误

> <img src="../../IMG2/Screenshot 2024-05-13 200310.png">

原因是字符串在编码前不能进行哈希运算，所以需要在字符串后进行编码，将所有的字符串后加上 `.encode("utf-8")` ，注意 `chr()` 也要加

```py
import hashlib   
for i in range(32,127):
    for j in range(32,127):
        for k in range(32,127):
            m=hashlib.md5()
            m.update('TASC'.encode("utf-8")+chr(i).encode("utf-8")+'O3RJMV'.encode("utf-8")+chr(j).encode("utf-8")+'WDJKX'.encode("utf-8")+chr(k).encode("utf-8")+'ZM'.encode("utf-8"))
            des=m.hexdigest()
            if 'e9032' in des and 'da' in des and '911513' in des:
                print(des)
```

- 运行得到结果

> <img src="../../IMG2/Screenshot 2024-05-13 200525.png">