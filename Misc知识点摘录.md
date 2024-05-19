# Misc

`更新时间 2024-5-15`

---

# 压缩包

无加密：`50 4B 03 04` 两个byte后为 `00 00` ， `50 4B 01 02` 后四个byte为 `00 00`

伪加密：`50 4B 03 04` 两个byte后为 `00 00` ， `50 4B 01 02` 后四个byte为 `09 00`

真加密：`50 4B 03 04` 两个byte后为 `09 00` ， `50 4B 01 02` 后四个byte为 `09 00`

# 图片

## 修改图片宽高

文件需要修改正确的宽高才能正常显示，查看文件的CRC值进行计算，如果计算得到的值有误，则直接进行爆破

## 文件头尾

常见文件头尾

```
JPEG (jpg)，
文件头：FFD8FF　　 文件尾：FF D9　
　　　　　　　　　　　　　　
PNG (png)， 　　
文件头：89504E47　 文件尾：AE 42 60 82
GIF (gif)， 　　
文件头：47494638　 文件尾：00 3B
ZIP Archive (zip)，
文件头：504B0304　　 文件尾：50 4B
TIFF (tif)， 　
文件头：49492A00
Windows Bitmap (bmp)， 　
文件头：424D
　　　　　　
CAD (dwg)， 　
文件头：41433130　
　　　　　　　　　　　　　　　　　　　　　
Adobe Photoshop (psd)，
文件头：38425053　
　　　　　　　　　　　　　　　　　　　　　
Rich Text Format (rtf)，
文件头：7B5C727466　
　　　　　　　　　　　　　　　　　　　
XML (xml)，
文件头：3C3F786D6C　
　　　　　　　　　　　　　　　　　　　
HTML (html)，
文件头：68746D6C3E
Email [thorough only] (eml)，
文件头：44656C69766572792D646174653A
Outlook Express (dbx)，
文件头：CFAD12FEC5FD746F
Outlook (pst)，
文件头：2142444E
MS Word/Excel (xls.or.doc)，
文件头：D0CF11E0
MS Access (mdb)，
文件头：5374616E64617264204A
WordPerfect (wpd)，
文件头：FF575043
Adobe Acrobat (pdf)，
文件头：255044462D312E
Quicken (qdf)，
文件头：AC9EBD8F
Windows Password (pwl)，
文件头：E3828596
RAR Archive (rar)，
文件头：52617221
Wave (wav)， 文件头：57415645
AVI (avi)， 文件头：41564920
Real Audio (ram)， 文件头：2E7261FD
Real Media (rm)， 文件头：2E524D46
MPEG (mpg)， 文件头：000001BA
MPEG (mpg)， 文件头：000001B3
Quicktime (mov)， 文件头：6D6F6F76
Windows Media (asf)， 文件头：3026B2758E66CF11
MIDI (mid)， 文件头：4D546864
```

## 字节流

使用文本格式打开图片，文本流中可能会存在key、flag、password等关键字

## 文件包含

一张图片可能包含两张图片，在kali中使用 `foremost` 或者 `binwalk` 查看

## 隐写

### LSB隐写

修改图片的最低有效位，每个像素点都可以保留一位的信息，每八位组成一组，因此使用工具提取每个像素点的每一位最低有效位即可

### 