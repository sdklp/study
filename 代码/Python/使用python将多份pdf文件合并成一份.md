你一定有过，或者身边的人曾经有过这样的需要，将多份pdf合并成一份。为了搞定这件事情，没少百度，下载了软件，或者使用在线工具，折腾很久才能搞定。现在，有了python，一切就不一样了，你可以自己写程序实现这个功能。

PyPDF2 是一个功能虽然不是很多，但却非常好用的第三方库，它提供了pdf文件的读写，拆分，合并等功能，使用pip命令进行安装

```text
pip3 install PyPDF2
```

下面是一份合并文件的示例代码

```python3
import os
from PyPDF2 import PdfFileMerger

target_path = '/Users/kwsy/Desktop'
pdf_lst = [f for f in os.listdir(target_path) if f.endswith('.pdf')]
pdf_lst = [os.path.join(target_path, filename) for filename in pdf_lst]

file_merger = PdfFileMerger()
for pdf in pdf_lst:
    file_merger.append(pdf)     # 合并pdf文件

file_merger.write("/Users/kwsy/Desktop/merge.pdf")
```

1. 使用os.listdir方法获取制定目录下的所有pdf文件名称
2. 使用os.path.join方法拼接成绝对路径
3. 创建PdfFileMerger对象，这是专门用来合并pdf文件的对象
4. 将所有文件append
5. 最后，使用write方法将所有pdf文件写入到一个文件

python是如此强大，我们所需要做的，就是掌握它，并融入到python社区，利用搜索引擎获取所需要的第三方开源库去做自己想做的事情。