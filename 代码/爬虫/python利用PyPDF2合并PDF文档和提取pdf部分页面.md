#合并

```python
 -*- coding:utf-8*-
# 利用PyPDF2模块合并同一文件夹下的所有PDF文件
# 只需修改存放PDF文件的文件夹变量：file_dir 和 输出文件名变量: outfile



import os
from PyPDF2 import PdfFileReader, PdfFileWriter
import time



# 使用os模块的walk函数，搜索出指定目录下的全部PDF文件
# 获取同一目录下的所有PDF文件的绝对路径
def getFileName(filedir):
    file_list = [os.path.join(root, filespath) \
                 for root, dirs, files in os.walk(filedir) \
                 for filespath in files \
                 if str(filespath).endswith('pdf')
                 ]
    return file_list if file_list else []
# 合并同一目录下的所有PDF文件
def MergePDF(filepath, outfile):
    output = PdfFileWriter()
    outputPages = 0
    pdf_fileName = getFileName(filepath)
    if pdf_fileName:
        for pdf_file in pdf_fileName:
            print("路径：%s"%pdf_file)
            # 读取源PDF文件
            input = PdfFileReader(open(pdf_file, "rb"))
            # 获得源PDF文件中页面总数
            pageCount = input.getNumPages()
            outputPages += pageCount
            print("页数：%d"%pageCount)
            # 分别将page添加到输出output中
            for iPage in range(pageCount):
                output.addPage(input.getPage(iPage))
        print("合并后的总页数:%d."%outputPages)
        # 写入到目标PDF文件
        outputStream = open(os.path.join(filepath, outfile), "wb")
        output.write(outputStream)
        outputStream.close()
        print("PDF文件合并完成！")
    else:
        print("没有可以合并的PDF文件！")
        
# 主函数
def main():
    time1 = time.time()
    # file_dir = r'E:\my_projects\python_projects\my tool packages\MergePDF'      # 存放PDF的原文件夹
    # file_dir = r'E:\my_projects\python_projects\my tool packages\MergePDF\paper'  # 存放PDF的原文件夹
    # file_dir = r'E:\my_projects\python_projects\my tool packages\MergePDF\reward'  # 存放PDF的原文件夹
    file_dir = r'E:\my_projects\python_projects\my tool packages\MergePDF\xx'  # 存放PDF的原文件夹
    # outfile = "works2xx.pdf" # 输出的PDF文件的名称
    # outfile = "reward2xx.pdf"  # 输出的PDF文件的名称
    outfile = "xx.pdf"  # 输出的PDF文件的名称
    MergePDF(file_dir, outfile)
    time2 = time.time()
    print('总共耗时：%s s.' %(time2 - time1))

main()

```

#提取

```python
from PyPDF2 import PdfFileReader, PdfFileWriter
def extract_pdf(file, page_begin=1, page_end=5):
    pdf = PdfFileReader(file)
    writer = PdfFileWriter()
    pages_num = page_end - page_begin + 1
    for i in range(pages_num):
        writer.addPage(pdf.getPage(i + page_begin - 1))
    file_split = 'copy2'+file
    with open(file_split, 'wb') as file_out:
        writer.write(file_out)
if __name__ == '__main__':
    page_begin = 2   # 起始页码(包括)
    page_end = 5     # 结束页码(包括)
    file_name = 'PhaseDNNpdeFinal.pdf'  # 要拆分的文件名称
    extract_pdf(file_name, page_begin, page_end)
```

 