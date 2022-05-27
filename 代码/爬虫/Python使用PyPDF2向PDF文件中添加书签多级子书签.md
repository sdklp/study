**PyPDF2的安装**：

```python
pip install PyPDF2
```

**关键语法**：

```python
from PyPDF2 import PdfFileReader as reader, PdfFileWriter as writer

pdf_in = reader(self.path_pdf)   # 读取PDF文件
self.pdf_out = writer()          # 创建一个可写的PDF对象

pdf_in.getNumPages()   # 获取PDF页数

self.pdf_out.addPage(pdf_in.getPage(page))  # 向可写PDF对象中追加一页

# 参数含义：                      书签名 指向页数  父书签  颜色   加粗   斜体   缩放类型  缩放的参数值   
parent = self.pdf_out.addBookmark(title, page, parent0, None, False, False, "/XYZ", None, None, None)  # 这个缩放组合可保持原缩放

self.pdf_out.write(open('new.pdf', 'wb'))   # 保存该可写pdf对象到文件
    注：windows下路径为/时ok, 如果路径为\则可能保存到异常位置；
       为防出现此问题，可使用os.chdir(current_path)切换工作目录为当前目录
       

```

**说明**：



- 关于把PDF Reader对象转换为Writer对象也许有更简单的方法，而不是通过一页一页地.addPage()
- 关于.addBookmark()的fit参数，可以查看Adobe家的一个手册：PDF Reference 1.7中的Table 8.2, 有具体说明

**根据自己需求编写可运行的程序，生成的PDF书签如下**：

- 章级书签：加粗
- 节级书签：是章级书签的子书签，可折叠
- 点击另一个书签时，能保持当前缩放比例不变，省的来回调整。
- 待改进：一页中有多个标题时，无法精确定位各标题的位置

![img](https://codeantenna.com/image/https://img-blog.csdnimg.cn/20200819232016229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIyMDk3Ng==,size_16,color_FFFFFF,t_70#pic_center)