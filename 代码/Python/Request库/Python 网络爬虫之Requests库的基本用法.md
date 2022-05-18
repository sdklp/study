# Python 网络爬虫之Requests库的基本用法

官网地址：Requests: HTTP for Humandocs.python-requests.org
安装方法：`pip install requests`
测试1：
`import requests r = requests.get('www.baidu.com')`
Requests库的7个主要方法：

| `requests.request()` | 构造一个请求，支撑以下各方法的基础方法         |
| -------------------- | ---------------------------------------------- |
| `requests.get()`     | 获取HTML页面的主要方法，对应于HTTP的GET        |
| `requests.head()`    | 获取HTML页面头信息的主要方法，对应于HTTP的HEAD |
| `requests.post()`    | 向HTML网页提交POST请求的方法，对应HTTP的POST   |
| `requests.put()`     | 向HTML网页提交PUT请求的方法，对应于HTTP的PUT   |
| `requests.patch()`   | 向HTML网页提交局部修改的方法，对应HTTP的PATCH  |
| `requests.delete()`  | 向HTML网页提交删除，对应HTTP的DELETE           |

`requests.request(method,url,**kwargs)`
`kwargs`:控制访问的参数，均为可选项

1.`params`：字典或者字节序列，作为参数增加到URL中

```
>>> kv = {'key1':'value1','key2':'value2'}
>>> r = requests.request("GET",'http://python123.io/ws',params=kv)
>>> print(r.url)
https://python123.io/ws?key1=value1&key2=value2
```

2.`data`:字典、字节序列或文件对象，作为Request的内容

```
>>> kv = {'key1':'value1','key2':'value2'}
>>> r = requests.request('POST','http://python123.io/ws',data=kv)
>>> body = "python"
>>> r = requests.request('POST','http://python123.io/ws',data=body)
```

3.`JSON`:JSON格式的数据，作为Request的内容

```
>>> kv = {'key1':'value1'}
>>> r = requests.request('POST','http://python123.io/ws',json=kv)
```

4.`headers`:字典，HTTP定制头

```
>>> hd = {'user-agent':'Chrome/10'}
>>> r = requests.request('POST','http://python123.io/ws',headers=hd)
```

5.`cookies`:字典或CookieJar,Request中的cookie
6.`auth`:元祖，支持HTTP认证功能
7.`files`:字典类型，传输文件

```
>>> fs = {'file':open('data.xls','rb')}
>>> r = requests.request('POST','http://python123.io/ws',files=fs)
```

8.`timeout`:设定超时时间，秒为单位
`>>> r = requests.request("GET",'http://www.baidu.com',timeout=10)`
9.`proxies`:字典类型，设定访问代理服务器，可以增加登录认证

```
pxs = {'http':'http://user:pass@10.10.10.1:1234','https':'https://10.10.10.1:4321'}
r = requests.request('GET','http://www.baidu.com',proxies = pxs)
```

10.`allow_redirects`:`True`/`False`,默认为`True`,重定向开关
11.`stream`:`True`/`False`,默认为`True`,获取内容立即下载开关
12.`verify`:`True`/`False`,默认为`True`,认证SSL证书开关
13.`cert`:保存本地SSL证书路径

**`requests.get()`方法使用：**
`r = requests.get(url,params=None,**kwargs)`
`url`:页面的URL链接
`params`：URL中的额外参数，字典或者字节流格式
`**kwargs`：12个控制访问的参数
**其他requests方法的使用和requests.request方法一样**

Response对象的属性
`r = requests.get(url)` 构造一个向服务器请求资源的Request对象，返回一个Response对象。

```
\>>> import requests

\>>> r = requests.get("(http://www.baidu.com/)")

\>>> print(r.status_code)

200

\>>> type(r)

<class 'requests.models.Response'>

\>>> r.headers

{'Cache-Control': 'private, no-cache, no-store, proxy-revalidate, no-transform', 'Connection': 'keep-alive', 'Content-Encoding': 'gzip', 'Content-Type': 'text/html', 'Date': 'Tue, 11 Feb 2020 04:26:11 GMT', 'Last-Modified': 'Mon, 23 Jan 2017 13:27:56 GMT', 'Pragma': 'no-cache', 'Server': 'bfe/1.0.8.18', 'Set-Cookie': 'BDORZ=27315; max-age=86400; [domain=.baidu.com](http://domain=.baidu.com/); path=/', 'Transfer-Encoding': 'chunked'}
```

| 属性                  | 说明                                             |
| --------------------- | ------------------------------------------------ |
| `r.status_code`       | HTTP请求的返回状态，200表示连接成功，404表示失败 |
| `r.text`              | HTTP相应内容的字符串形式，即URL对应的页面内容    |
| `r.encoding`          | 从HTTP header中猜测的响应内容编码方式            |
| `r.apparent_encoding` | 从内容中分析出的响应内容编码方式（备选编码方式） |
| `r.content`           | HTTP响应内容的二进制形式                         |

**编码很重要**：如果网页编码方式是`ISO-8895-1`,那么用`r.text`查看的是乱码。`r.encoding`只是分析网页的头部，猜测编码方式，而`r.apparent_encoding`则是实实在在根据网页内容分析编码方式。所以，在爬虫程序中，经常使用`r.encoding = r.apparent_encoding`,来直接获取网页的编码。
**爬取网页的通用代码框架：**

| 异常                        | 说明                                        |
| --------------------------- | ------------------------------------------- |
| `requests.ConnectionError`  | 网络连接错误异常，如DNS查询失败、拒绝连接等 |
| `requests.HTTPError`        | HTTP错误异常                                |
| `requests.URLRequired`      | URL缺失异常                                 |
| `requests.TooManyRedirects` | 超过最大重定向次数，产生重定向异常          |
| `requests.ConnectTimeout`   | 连接远程服务器超时异常                      |
| `requests.Timeout`          | 请求URL超时，产生超时异常                   |

Response异常的捕捉

| 异常                   | 说明                                      |
| ---------------------- | ----------------------------------------- |
| `r.raise_for_status()` | 如果不是200，产生异常`requests.HTTPError` |

通用代码框架如下：

```
import requests
def  getHTMLText(url):
    try:
        r = requests.get(url,timeout = 30) #30秒超时异常
        r.raise_for_status()#如果r返回的状态不是200，则引发HTTPError异常
        r.encoding = r.apparent_encoding
        return r.text
    except:
        return "产生异常"

if __name__ == "__main__":
    url = "http://www.baidu.com"
    print(getHTMLText(url))
    
```

错误测试：

```
>>> u = "http://b.com"
>>> print(getHTMLText(u))
产生异常
```

HTTP协议---超文本传输协议，请求响应、无状态的模式
URL格式 `http://host[:port][path]`
`host`：合法的Internet主机域名或者IP地址
`[:port]`：端口号，缺省端口为80
`[path]`：请求资源的路径

**HTTP协议对资源的操作**

| 方法   | 说明                                                      |
| ------ | --------------------------------------------------------- |
| GET    | 请求获取URL位置的资源                                     |
| HEAD   | 请求获取URL位置资源的响应消息报告，即获得该资源的头部信息 |
| POST   | 请求向URL位置的资源后附加新的数据                         |
| PUT    | 请求向URL位置存储一个资源，覆盖原URL位置的资源            |
| PATCH  | 请求局部更新URL位置的资源，即改变该处资源的**部分**内容   |
| DELETE | 请求删除URL位置存储的资源                                 |

Requests库的方法与HTTP协议是一一对应的