selenium点击链接从列表页进入内容页面抓取内容，适合无a标签，点击div跳转



### 目标

从列表页中点击div，进入详细内容页，并采集内容页数据。且无a标签。要用句柄间切换，共4步。

```
`import` `requests``import` `time``from` `selenium ``import` `webdriver``from` `selenium.webdriver.chrome.options ``import` `Options` `url``=``'https://hotel.tuniu.com/list/414p0s0b0?checkindate=2021-06-06&checkoutdate=2021-06-07&cityName=%E5%8E%A6%E9%97%A8'``options``=``Options()``#options.headless=True``options.add_argument(``'--proxy-server=http://127.0.0.1:3215'``)``driver ``=` `webdriver.Chrome(executable_path``=``'chromedriver'``, options``=``options)``driver.get(url)``elements ``=` `driver.find_elements_by_css_selector(``'[class="detail-btn f-s"]'``)``#1.the current page handle and its the main list page``h1``=``driver.window_handles``#print('h1=',h1)``#print(elements)``for` `element ``in` `elements:``    ``print``(element)``    ``element.click()``    ``time.sleep(``10``)``    ``h2``=``driver.window_handles``    ``#print("h2=",h2)``    ``#2.go to the detail page and its the content page``    ``driver.switch_to.window(h2[``1``])``    ``currentPageUrl``=``driver.current_url``    ``driver.get(currentPageUrl)``    ``print``(driver.title)``    ``driver.close()``#3.close current page``    ``driver.switch_to.window(h2[``0``])``#4.back to main list page``       ` `driver.quit()`
```