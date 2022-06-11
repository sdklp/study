找了一个新闻网站练习爬虫抓取，目标：逐一点击目录标题，进入详细新闻页面，抓取子页面的标题和正文内容并打印出来，返回目录标题页，点击下一篇文章。**注：没有新开窗口，是在原窗口实现跳转。新开窗口进行抓取看下一篇文章。**

试了很多种方法都抓取不到class=rightContent下面每个a标签里的href链接，开始思考是不是因为href链接都放在li列表里面导致。

![img](https://img-blog.csdn.net/20180923084719906?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMjUxNDQz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

后面终于试到怎么获取这些在列表li里的href链接了：首先第一步先抓取到所有a标签，这里用的是绝对路径，比较方便，直接右键copy→xpath。这样抓取出来的是一个列表links：

```python
links=driver.find_elements_by_xpath("/html/body/div[5]/div[4]/ul/li/a") 
```

通过for link in links遍历所有抓取出来的a标签内容，然后再用get_attribute()函数来获取每一个link的href属性，得到的是a标签对应的目标页面的URL：

```python
link.get_attribute('href')
```

这样就能获得每一个子页面地址。但要注意，这里抓取出来的是地址的字符串，不能点击。相当于在word里面输入网址，但是没按回车制造超链接，使网址变成了普通文字。这时候不能用click()，应该要用driver.get()来让浏览器加载这个url，相当于把网址的字符串复制到浏览器网址栏直接打开网址：

```python
driver.get(url) 
```

打开子页面成功抓取第一篇的标题和正文退回目录页后，发现停住不动了。这时候是因为页面发生跳转后，所有元素的信息都变更了。因此，需要在每次回到目录页面后重新抓取a标签，同时又必须接着上次点击后一个的元素继续点击。使用遍历的方法来实现这个for循环，完整代码如下：

```python
from selenium import webdriver
import time
driver=webdriver.Chrome()
driver.get("http://www.spic.com.cn/2018news/zhxx/")
time.sleep(3)               #留出加载时间
links=driver.find_elements_by_xpath("/html/body/div[5]/div[4]/ul/li/a")  #获取到所有a标签，组成一个列表
length=len(links)           #列表的长度，一共有多少个a标签
for i in range(0,length):   #遍历列表的循环，使程序可以逐一点击
    links=driver.find_elements_by_xpath("/html/body/div[5]/div[4]/ul/li/a")    #在每次循环内都重新获取a标签，组成列表
    link=links[i]           #逐一将列表里的a标签赋给link
    url=link.get_attribute('href')  #提取a标签内的链接，注意这里提取出来的链接是字符串
    driver.get(url)         #不能用click，因为click点击字符串没用，直接用浏览器打开网址即可
    time.sleep(1)           #留出加载时间
    title=driver.find_element_by_xpath("//div[@class='articleTitle']").text   #.text的意思是指输出这里的纯文本内容
    print(title)
    content=driver.find_element_by_xpath("//div[@class='TRS_Editor']").text
    print(content)
    print("\n")
    driver.back()           #后退，返回原始页面目录页
    time.sleep(1)           #留出加载时间
print(length)               #打印列表长度，即有多少篇文章
```

