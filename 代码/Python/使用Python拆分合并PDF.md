**使用Python操作PDF！**

**主要内容有：1、PDF拆分；2、PDF合并。**

在工作中，难免会和PDF打交道，所以掌握一点处理PDF的技能非常有必要，本文将介绍几个常用的功能。

# **PDF拆分**

很多时候，获取的PDF很长，我们如果想要截取其中某些页面那么怎么处理呢？有很多的工具可以完成类似的操作，我们用Python也能做到类似的事情。并且用Python来做类似的处理，非常便于我们后面做一些批处理工具。

直接上代码吧！

```javascript
from PyPDF2 import PdfFileWriter, PdfFileReader

def pdf_split(pdf_in,pdf_out,start,end):
    # 初始化一个pdf
    output = PdfFileWriter()
    # 读取pdf
    with open(pdf_in,'rb') as in_pdf:
        pdf_file = PdfFileReader(in_pdf)
        # 从pdf中取出指定页
        for i in range(start, end):
            output.addPage(pdf_file.getPage(i))
        # 写出pdf
        with open(pdf_out,'ab') as out_pdf:
            output.write(out_pdf)

if __name__ == '__main__':
    pdf_in  = '待分割pdf'
    pdf_out = '分割后pdf'
    s,e     = 起始页，结束页
    pdf_manage(pi, po, s, e)
```

# **PDF合并**

与pdf拆分相对的，是pdf的合并。使用Python也能轻松完成，不早了，不废话了，还是直接上代码吧！

```javascript
from PyPDF2 import PdfFileReader,PdfFileMerger

def pdf_merger(in_pdfs,out_pdf):
    # 初始化
    merger = PdfFileMerger()
    # 循环，合并
    for in_pdf in in_pdfs:
        with open(in_pdf,'rb') as pdf:
            merger.append(PdfFileReader(pdf))
    merger.write(out_pdf)

if __name__ == '__main__':
    in_pdfs = ['放要合并的PDF文件名称，注意顺序']
    out_pdf = '输出文件'
    pdf_merger(in_pdfs, out_pdf)
```