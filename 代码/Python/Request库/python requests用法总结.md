# python requests用法总结

`requests`是一个很实用的[Python](http://lib.csdn.net/base/python) HTTP客户端库，编写爬虫和测试服务器响应数据时经常会用到。可以说，**Requests 完全满足如今网络的需求**

本文全部来源于官方文档 http://docs.python-requests.org/en/master/

安装方式一般采用$ pip install requests。其它安装方式参考官方文档

 

HTTP - requests

 

**import** requests

 

GET请求

 

r = requests.get('http://httpbin.org/get')

 

传参

\>>> payload = **{**'key1'**:** 'value1'**,** 'key2'**:** 'value2', 'key3'**:** None**}**
\>>> r = requests.get**(**'http://httpbin.org/get'**,** params=payload**)**

 

http://httpbin.org/get?key2=value2&key1=value1

 

Note that any dictionary key whose value is None will not be added to the URL's query string.

 

参数也可以传递列表

 

\>>> payload = **{**'key1'**:** 'value1'**,** 'key2'**: [**'value2'**,** 'value3'**]}**

\>>> r = requests.get**(**'http://httpbin.org/get'**,** params=payload**)**
\>>> **print****(**r.url**)**
http://httpbin.org/get?key1=value1&key2=value2&key2=value3

r.text 返回headers中的编码解析的结果，可以通过r.encoding = 'gbk'来变更解码方式

r.content返回二进制结果

r.json()返回JSON格式，可能抛出异常

r.status_code

r.raw返回原始socket respons，需要加参数stream=True

 

\>>> r = requests.get**(**'https://api.github.com/events'**,** stream=True**)**

\>>> r.raw
<requests.packages.urllib3.response.HTTPResponse object at 0x101194810>

\>>> r.raw.read**(**10**)**
'\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'

将结果保存到文件，利用r.iter_content()

 

**with** open**(**filename**,** 'wb'**)** **as** fd**:**
  **for** chunk **in** r.iter_content**(**chunk_size**):**
    fd.write**(**chunk**)**

 

传递headers

 

\>>> headers = **{**'user-agent'**:** 'my-app/0.0.1'**}**
\>>> r = requests.get**(**url**,** headers=headers**)**

 

传递cookies

 

\>>> url = 'http://httpbin.org/cookies'

\>>> r = requests.get**(**url**,** cookies=dict**(**cookies_are='working'**))**
\>>> r.text
'{"cookies": {"cookies_are": "working"}}'

 

 

POST请求

 

传递表单

r = requests.post**(**'http://httpbin.org/post'**,** data = **{**'key'**:**'value'**})**

 

通常，你想要发送一些编码为表单形式的数据—非常像一个HTML表单。 要实现这个，只需简单地传递一个字典给 data 参数。你的数据字典 在发出请求时会自动编码为表单形式:

 

 

\>>> payload = **{**'key1'**:** 'value1'**,** 'key2'**:** 'value2'**}**

\>>> r = requests.post**(**"http://httpbin.org/post"**,** data=payload**)**
\>>> **print****(**r.text**)**
{
 ...
 "form": {
  "key2": "value2",
  "key1": "value1"
 },
 ...
}

很多时候你想要发送的数据并非编码为表单形式的。如果你传递一个 string 而不是一个dict ，那么数据会被直接发布出去。

 

\>>> url = 'https://api.github.com/some/endpoint'
\>>> payload = **{**'some'**:** 'data'**}**

 

\>>> r = requests.post**(**url**,** data=json.dumps(payload)**)**

或者

\>>> r = requests.post**(**url**,** json=payload**)**

 

 

传递文件

 

url = 'http://httpbin.org/post'
\>>> files = **{**'file'**:** open**(**'report.xls'**,** 'rb'**)}**

\>>> r = requests.post**(**url**,** files=files**)**

配置files，filename, content_type and headers

files = **{**'file'**: (**'report.xls'**,** open**(**'report.xls'**,** 'rb'**),** 'application/vnd.ms-excel'**, {**'Expires'**:** '0'**})}**

 

files = **{**'file'**: (**'report.csv'**,** 'some,data,to,send\nanother,row,to,send\n'**)}**

 

响应

 

r.status_code

r.heards

r.cookies

 

 

跳转

 

By default Requests will perform location redirection for all verbs except HEAD.

 

\>>> r = requests.get**(**'http://httpbin.org/cookies/set?k2=v2&k1=v1'**)**

\>>> r.url
'http://httpbin.org/cookies'

\>>> r.status_code
200

\>>> r.history
[<Response [302]>]

 

If you're using HEAD, you can enable redirection as well:

 

r=requests.head('http://httpbin.org/cookies/set?k2=v2&k1=v1',allow_redirects=**True**)

 

You can tell Requests to stop waiting for a response after a given number of seconds with the timeoutparameter:

 

requests.get**(**'http://github.com'**,** timeout=0.001**)**

 

 

高级特性

 

来自 <http://docs.python-requests.org/en/master/user/advanced/#advanced>

 

session，自动保存cookies，可以设置请求参数，下次请求自动带上请求参数

 

s = requests.Session**()**

s.get**(**'http://httpbin.org/cookies/set/sessioncookie/123456789'**)**
r = s.get**(**'http://httpbin.org/cookies'**)**

**print****(**r.text**)**
*# '{"cookies": {"sessioncookie": "123456789"}}'*

session可以用来提供默认数据，函数参数级别的数据会和session级别的数据合并，如果key重复，函数参数级别的数据将覆盖session级别的数据。如果想取消session的某个参数，可以在传递一个相同key，value为None的dict

 

s = requests.Session**()**
s.auth = **(**'user'**,** 'pass'**) #****权限认证**
s.headers.update**({**'x-test'**:** 'true'**})**

*# both 'x-test' and 'x-test2' are sent*
s.get**(**'http://httpbin.org/headers'**,** headers=**{**'x-test2'**:** 'true'**})**

函数参数中的数据只会使用一次，并不会保存到session中

 

如：cookies仅本次有效

r = s.get**(**'http://httpbin.org/cookies'**,** cookies=**{**'from-my'**:** 'browser'**})**

 

session也可以自动关闭

 

**with** requests.Session**()** **as** s**:**
  s.get**(**'http://httpbin.org/cookies/set/sessioncookie/123456789'**)**

 

响应结果不仅包含响应的全部信息，也包含请求信息

 

r = requests.get**(**'http://en.wikipedia.org/wiki/Monty_Python'**)**

r.headers

r.request.headers

 

 

SSL证书验证

 

 

Requests可以为HTTPS请求验证SSL证书，就像web浏览器一样。要想检查某个主机的SSL证书，你可以使用 verify 参数:

 

 

\>>> requests.get**(**'https://kennethreitz.com'**,** verify=True**)**
requests.exceptions.SSLError: hostname 'kennethreitz.com' doesn't match either of '*.herokuapp.com', 'herokuapp.com'

在该域名上我没有设置SSL，所以失败了。但Github设置了SSL:

\>>> requests.get**(**'https://github.com'**,** verify=True**)**
<Response [200]>

对于私有证书，你也可以传递一个CA_BUNDLE文件的路径给 verify 。你也可以设置REQUEST_CA_BUNDLE 环境变量。

 

\>>> requests.get**(**'https://github.com'**,** verify='/path/to/certfile'**)**

 

如果你将 verify 设置为False，Requests也能忽略对SSL证书的验证。

 

\>>> requests.get**(**'https://kennethreitz.com'**,** verify=False**)**
<Response [200]>

默认情况下， verify 是设置为True的。选项 verify 仅应用于主机证书。

你也可以指定一个本地证书用作客户端证书，可以是单个文件（包含密钥和证书）或一个包含两个文件路径的元组:

 

\>>> requests.get**(**'https://kennethreitz.com'**,** cert=**(**'/path/server.crt'**,** '/path/key'**))**
<Response [200]>

响应体内容工作流

 

默认情况下，当你进行网络请求后，响应体会立即被下载。你可以通过 stream 参数覆盖这个行为，推迟下载响应体直到访问 **Response.content** 属性:

 

tarball_url = 'https://github.com/kennethreitz/requests/tarball/master'
r = requests.get**(**tarball_url**,** stream=True**)**

此时仅有响应头被下载下来了，连接保持打开状态，因此允许我们根据条件获取内容:

 

**if** int**(**r.headers**[**'content-length'**])** < TOO_LONG**:**
 content = r.content
 ...

如果设置stream为True，请求连接不会被关闭，除非读取所有数据或者调用Response.close。

 

可以使用contextlib.closing来自动关闭连接：

 

 

**import** requests

**from** contextlib

**import** closing

tarball_url **=** 'https://github.com/kennethreitz/requests/tarball/master'

file **=** r'D:\Documents\WorkSpace\Python\Test\Python34Test\test.tar.gz'

 

**with** closing**(**requests**.**get**(**tarball_url**,** stream**=****True****))** **as** r**:**

**with** open**(**file**,** 'wb'**)** **as** f**:**

**for** data **in** r**.**iter_content**(**1024**):**

f**.**write**(**data**)**

 

Keep-Alive

 

来自 <http://docs.python-requests.org/en/master/user/advanced/>

 

同一会话内你发出的任何请求都会自动复用恰当的连接！

注意：只有所有的响应体数据被读取完毕连接才会被释放为连接池；所以确保将 stream设置为 False 或读取 Response 对象的 content 属性。

 

流式上传

Requests支持流式上传，这允许你发送大的数据流或文件而无需先把它们读入内存。要使用流式上传，仅需为你的请求体提供一个类文件对象即可:

读取文件请使用字节的方式，这样Requests会生成正确的Content-Length

**with** open**(**'massive-body'**,** 'rb'**)** **as** f**:**
  requests.post**(**'http://some.url/streamed'**,** data=f**)**

 

分块传输编码

 

对于出去和进来的请求，Requests也支持分块传输编码。要发送一个块编码的请求，仅需为你的请求体提供一个生成器

注意生成器输出应该为bytes

**def** gen**():**
  **yield** b'hi'
  **yield** b'there'

requests.post**(**'http://some.url/chunked'**,** data=gen**())**

For chunked encoded responses, it's best to iterate over the data using **Response.iter_content()**. In an ideal situation you'll have set stream=True on the request, in which case you can iterate chunk-by-chunk by calling iter_content with a chunk size parameter of None. If you want to set a maximum size of the chunk, you can set a chunk size parameter to any integer.

POST Multiple Multipart-Encoded Files

 

来自 <http://docs.python-requests.org/en/master/user/advanced/>

 

<input type="file" name="images" multiple="true" required="true"/>

 

To do that, just set files to a list of tuples of (form_field_name, file_info):

 

\>>> url = 'http://httpbin.org/post'
\>>> multiple_files = **[**
    ('images', ('foo.png', open('foo.png', 'rb'), 'image/png')),
    ('images', ('bar.png', open('bar.png', 'rb'), 'image/png'))]
\>>> r = requests.post**(**url**,** files=multiple_files**)**
\>>> r.text
{
 ...
 'files': {'images': 'data:image/png;base64,iVBORw ....'}
 'Content-Type': 'multipart/form-data; boundary=3131623adb2043caaeb5538cc7aa0b3a',
 ...
}

Custom Authentication

Requests allows you to use specify your own authentication mechanism.

Any callable which is passed as the auth argument to a request method will have the opportunity to modify the request before it is dispatched.

Authentication implementations are subclasses of requests.auth.AuthBase, and are easy to define. Requests provides two common authentication scheme implementations in requests.auth:HTTPBasicAuth and HTTPDigestAuth.

Let's pretend that we have a web service that will only respond if the X-Pizza header is set to a password value. Unlikely, but just go with it.

**from** requests.auth **import** AuthBase

**class** PizzaAuth**(**AuthBase**):**
  *"""Attaches HTTP Pizza Authentication to the given Request object."""*
  **def** __init__**(**self**,** username**):**
    *# setup any auth-related data here*
    self.username = username

**def** __call__**(**self**,** r**):**
    *# modify and return the request*
    r.headers**[**'X-Pizza'**]** = self.username
    **return** r

Then, we can make a request using our Pizza Auth:

\>>> requests.get**(**'http://pizzabin.org/admin'**,** auth=PizzaAuth**(**'kenneth'**))**
<Response [200]>

 

来自 <http://docs.python-requests.org/en/master/user/advanced/>

 

流式请求

 

r = requests.get**(**'http://httpbin.org/stream/20'**,** stream=True**)**

**for** line **in** r.iter_lines**():**

 

代理

 

If you need to use a proxy, you can configure individual requests with the proxies argument to any request method:

**import** requests

proxies = **{**
 'http'**:** '[http://10.10.1.10:3128](http://10.10.1.10:3128/)'**,**
 'https'**:** '[http://10.10.1.10:1080](http://10.10.1.10:1080/)'**,**
**}**

requests.get**(**'http://example.org'**,** proxies=proxies**)**

 

To use HTTP Basic Auth with your proxy, use the http://user:password@host/ syntax:

proxies = **{**'http'**:** 'http://user:pass@10.10.1.10:3128/'**}**

 

超时

 

 

If you specify a single value for the timeout, like this:

 

r = requests.get**(**'https://github.com'**,** timeout=5**)**

 

The timeout value will be applied to both the connect and the read timeouts. Specify a tuple if you would like to set the values separately:

 

r = requests.get**(**'https://github.com'**,** timeout=**(**3.05**,** 27**))**

 

If the remote server is very slow, you can tell Requests to wait forever for a response, by passing None as a timeout value and then retrieving a cup of coffee.

 

r = requests.get**(**'https://github.com'**,** timeout=None**)**