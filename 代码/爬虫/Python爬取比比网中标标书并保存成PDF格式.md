### 前言

本文的文字及图片来源于网络,仅供学习、交流使用,不具有任何商业用途,如有问题请及时联系我们以作处理。

### python开发环境

- python 3.6
- pycharm
- requests
- parsel
- pdfkit
- time
- 相关模块pip安装即可

### 目标网页分析

![Python爬取比比网中标标书并保存成PDF格式_JAVA](https://s6.51cto.com/images/blog/202012/31/2ceb5a0d81efc9df4e1ccfcf24a1b7f3.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

### 1、先从列表页中获取详情页的URL地址

是静态网站，可以直接请求网页获取数据

![Python爬取比比网中标标书并保存成PDF格式_JAVA_02](https://s7.51cto.com/images/blog/202012/31/f9c6443f2faa9bc8620d13a771aa9e73.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```html
for page in range(1, 31):
    url = 'https://www.bibenet.com/mfzbu{}.html'.format(page)
    headers = {

        'Referer': 'https://www.bibenet.com/mianfei/',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36'
    }
    response = requests.get(url=url, headers=headers)
    selector = parsel.Selector(response.text)
    urls = selector.css('body > div.wrap > div.clearFiex > div.col9.fl > div.secondary_box > table tr .fl a::attr(href)').getall()
    for page_url in urls:
    print(page_url)
```





### 2、从详情页中获取标题以及内容

```html
#Python学习交流QQ群：778463939
response_2 = requests.get(url=page_url, headers=headers)
selector_2 = parsel.Selector(response_2.text)
article = selector_2.css('.container').get()
title = selector_2.css('.detailtitle::text').get()
```





### 3、保存html网页数据并转成PDF

```html
html_str = """
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
{article}
</body>
</html>
"""
def download(article, title):
    html = html_str.format(article=article)
    html_path = 'D:\\python\\demo\\招标网\\文书\\' + title + '.html'
    pdf_path = 'D:\\python\\demo\\招标网\\文书\\' + title + '.pdf'
    with open(html_path, mode='wb', encoding='utf-8') as f:
        f.write(html)
        print('{}已下载完成'.format(title))
    # exe 文件存放的路径
    config = pdfkit.configuration(wkhtmltopdf='C:\\Program Files\\wkhtmltopdf\\bin\\wkhtmltopdf.exe')
    # 把 html 通过 pdfkit 变成 pdf 文件
    pdfkit.from_file(html_path, pdf_path, configuration=config)

```





### 运行实现效果

![Python爬取比比网中标标书并保存成PDF格式_JAVA_03](https://s7.51cto.com/images/blog/202012/31/78eda48494d789b5f4c03e4d46048ff1.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![Python爬取比比网中标标书并保存成PDF格式_JAVA_04](https://s9.51cto.com/images/blog/202012/31/e8417b1c5548c939cfab625afe4fd225.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
![Python爬取比比网中标标书并保存成PDF格式_JAVA_05](https://s8.51cto.com/images/blog/202012/31/8c23566e2d9a8146e01955f680da9416.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)





- 