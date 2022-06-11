selenium点击链接进入子页面抓取内容，适合有a标签实现页面跳转



### 目标

逐一点击目录标题，进入详细新闻页面，抓取子页面的标题和正文内容并打印出来，返回目录标题页，点击下一篇文章。注：没有新开窗口，是在原窗口实现跳转。

get_attribute()函数来获取每一个link的href属性，得到的是a标签对应的目标页面的URL。

打开子页面成功抓取第一篇的标题和正文退回目录页后，发现停住不动了。这时候是因为页面发生跳转后，所有元素的信息都变更了。因此，需要在每次回到目录页面后重新抓取a标签，同时又必须接着上次点击后一个的元素继续点击。使用遍历的方法来实现这个for循环，完整代码如下：

```
from` `selenium ``import` `webdriver
import` `time
 
driver``=``webdriver.Chrome()
driver.get(``"http://www.spic.com.cn/2018news/zhxx/"``)
time.sleep(``3``)               ``#留出加载时间
 
links``=``driver.find_elements_by_xpath(``"/html/body/div[5]/div[4]/ul/li/a"``)  ``#获取到所有a标签，组成一个列表
length``=``len``(links)           ``#列表的长度，一共有多少个a标签
 
for` `i ``in` `range``(``0``,length):   ``#遍历列表的循环，使程序可以逐一点击
    ``links``=``driver.find_elements_by_xpath(``"/html/body/div[5]/div[4]/ul/li/a"``)    ``#在每次循环内都重新获取a标签，组成列表
    ``link``=``links[i]           ``#逐一将列表里的a标签赋给link
    ``url``=``link.get_attribute(``'href'``)  ``#提取a标签内的链接，注意这里提取出来的链接是字符串
    ``driver.get(url)         ``#不能用click，因为click点击字符串没用，直接用浏览器打开网址即可
    ``time.sleep(``1``)           ``#留出加载时间
    ``title``=``driver.find_element_by_xpath(``"//div[@class='articleTitle']"``).text   ``#.text的意思是指输出这里的纯文本内容
    ``print``(title)
    ``content``=``driver.find_element_by_xpath(``"//div[@class='TRS_Editor']"``).text
    ``print``(content)
    ``print``(``"\n"``)
    ``driver.back()           ``#后退，返回原始页面目录页
    ``time.sleep(``1``)           ``#留出加载时间
 
print``(length)               ``#打印列表长度，即有多少篇文章
```