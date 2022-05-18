# python操作word文档

python操作word我们需要用到python-docx这个库。

**安装命令：**



```cmd
pip install python-docx
```

[官方文档地址](https://link.jianshu.com/?t=http%3A%2F%2Fpython-docx.readthedocs.io%2Fen%2Flatest%2F)[http://python-docx.readthedocs.io/en/latest/](https://link.jianshu.com/?t=http%3A%2F%2Fpython-docx.readthedocs.io%2Fen%2Flatest%2F)
**官方demo:**



```python
from docx import Document
from docx.shared import Inches

document = Document()
# 添加标题
document.add_heading('Document Title', 0)
#添加文本
p = document.add_paragraph('A plain paragraph having some ')
# 设置粗体
p.add_run('bold').bold = True
p.add_run(' and some ')
# 设置斜体
p.add_run('italic.').italic = True
# 添加一级标题
document.add_heading('Heading, level 1', level=1)
# 添加样式
document.add_paragraph('Intense quote', style='IntenseQuote')

document.add_paragraph(
    'first item in unordered list', style='ListBullet'
)
document.add_paragraph(
    'first item in ordered list', style='ListNumber'
)
# 添加图片
document.add_picture('monty-truth.png', width=Inches(1.25))
# 添加表格
table = document.add_table(rows=1, cols=3)
hdr_cells = table.rows[0].cells
hdr_cells[0].text = 'Qty'
hdr_cells[1].text = 'Id'
hdr_cells[2].text = 'Desc'
for item in recordset:
    row_cells = table.add_row().cells
    row_cells[0].text = str(item.qty)
    row_cells[1].text = str(item.id)
    row_cells[2].text = item.desc

#添加分页符
document.add_page_break()
#保存文档
document.save('demo.docx')
```

效果如下：



![img](https://upload-images.jianshu.io/upload_images/2597553-0125031e5f05d10f.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/375/format/webp)

image

看了上面的官方demo就能直接上手使用了。
不过我建议自己可以按需封装一下方法



```python
from docx import Document
from docx.shared import Inches
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.shared import Pt
from docx.oxml.ns import qn 

document = Document()
#设置文档默认字体
document.styles['Normal'].font.name = u'微软雅黑' 
document.styles['Normal']._element.rPr.rFonts.set(qn('w:eastAsia'), u'微软雅黑')
title = document.add_paragraph()
#大标题居中
title.paragraph_format.alignment = WD_ALIGN_PARAGRAPH.CENTER

# 参数
#文档对象，文字内容，文字大小，文字样式（目前就只判断了粗体）
def writeP(document, content, size, style = None):
    p = document.add_paragraph()
    run = p.add_run(content)
    font = run.font
    font.size = Pt(size)
    if style == 'bold':
        font.bold = True
```