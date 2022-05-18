# python中requests库使用

1、发送请求

  1）get请求

```
import requests
r = requests.get('    #r为Response对象
```

  2）post请求

```
import requests
r = requests.post('http://httpbin.org/post', data = {'key':'value'})
```

  3）其他请求

```
import requests
r = requests.put('http://httpbin.org/put', data = {'key':'value'})
r = requests.delete('http://httpbin.org/delete')
r = requests.head('http://httpbin.org/get')
r = requests.options('http://httpbin.org/get')
```

2、传递URL参数

  有的URL 查询需要传递一些数据。如果你是手工构建 URL，那么数据会以键/值对的形式置于 URL 中，跟在一个问号的后面。例如， `httpbin.org/get?key=val`。 Requests 允许你使用 `params` 关键字参数，以一个字符串字典来提供这些参数。

```
import requests
payload = {'key1': 'value1', 'key2': 'value2'}  #注意字典里值为 None 的键都不会被添加到 URL 的查询字符串里
r = requests.get("http://httpbin.org/get", params=payload)
print(r.url)                                    #http://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

3、响应内容

  1）文本响应内容

​    Requests 会自动解码来自[服务器](https://www.yisu.com/)的内容。大多数 unicode 字符集都能被无缝地解码。

```
 import requests
 r = requests.get('https://api.github.com/events')
 r.text
```

   请求发出后，Requests 会基于 HTTP 头部对响应的编码作出有根据的推测。当你访问 `r.text` 之时，Requests 会使用其推测的文本编码。你可以找出 Requests 使用了什么编码，并且能够使用`r.encoding` 属性来改变它

```
r.encoding                 #'utf-8'
r.encoding = 'ISO-8859-1'
```

  2）二进制响应内容

​    以字节的方式访问请求响应体，对于非文本请求：

```
r.contentb          #'[{"repository":{"open_issues":0,"url":"https://github.com/...
```

​    Requests 会自动为你解码 `gzip` 和 `deflate` 传输编码的响应数据。

​    例如，以请求返回的二进制数据创建一张图片，你可以使用如下代码：

```
from PIL import Image
from io import BytesIO
i = Image.open(BytesIO(r.content))
```

   3）json响应内容

​    Requests 中也有一个内置的 JSON ×××，助你处理 JSON 数据：

```
import requests
r = requests.get('https://api.github.com/events')
r.json()        #[{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...
```

​    如果 JSON 解码失败， `r.json()` 就会抛出一个异常。例如，响应内容是 401 (Unauthorized)，尝试访问 `r.json()` 将会抛出 `ValueError: No JSON object could be decoded` 异常。

​    PS：成功调用 `r.json()` 并不意味着响应的成功。有的服务器会在失败的响应中包含一个 JSON 对象（比如 HTTP 500 的错误细节）。这种 JSON 会被解码返回。要检查请求是否成功，请使用 `r.raise_for_status()` 或者检查 `r.status_code` 是否和你的期望相同。

  4）原始响应内容

​    在罕见的情况下，你可能想获取来自服务器的原始套接字响应，那么你可以访问 `r.raw`（确保在初始请求中设置了 `stream=True）`

```
r = requests.get('https://api.github.com/events', stream=True)
r.raw            #<requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
r.raw.read(10)   #'\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'
```

​    但一般情况下，你应该以下面的模式将文本流保存到文件：

```
with open(filename, 'wb') as fd:
    for chunk in r.iter_content(chunk_size):
        fd.write(chunk)
```

​    使用 `Response.iter_content` 将会处理大量你直接使用 `Response.raw` 不得不处理的。 当流下载时，上面是优先推荐的获取内容方式。

# 2.requests用法如下：

1. 导入re模块：import requests
2. 获取响应数据与解码：
   a. 获取响应数据代码：response = requests.get(url)
   b. 解码代码：html = response.content.decode(‘utf-8’)
   c. 其中，url代表要访问的网址，utf-8表示解码格式
3. 案例演示：

```
import requests
# 获取响应数据
response = requests.get('https://www.baidu.com')
# 对响应数据进行解码
html = response.content.decode('utf-8') 
print(html)
```