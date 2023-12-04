# BUU UPLOAD COURSE 1

页面提示上传文件

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/7`$TP4GC{90X%DLT~S$K9YY.png?raw=true">

先随便上传一个文件，这里我上传一个压缩包，上传完成后提示了上传路径

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/[AV$R(TTDOP7DX)87BK0[8K.png?raw=true">

查看url栏，可能存在文件包含漏洞，尝试打开这个文件，成功打开

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/)$Z(P]2U@RL2%QY]M{39Z)4.png?raw=true">

根据报错判断，服务器直接把上传的文件转换为jpg格式，然后把文件作为php代码执行，上传一句话木马，成功打开

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/A4343XX`91P{VDHN}YZYYSC.png?raw=true">

使用蚁剑连接

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/Y(RH3WTK~WL70B5{0NM8RI3.png?raw=true">

回到根目录，找到flag

> <img src="https://github.com/Ki1z/CTF/blob/main/IMG/W@J{@%6UGFRXF{(LZ}IJ))C.png?raw=true">