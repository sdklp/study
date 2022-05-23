# scrapy-redis记录，重写make_requests_from_url实现start_url

起因是最近爬了某电商商品，因为用了scrapy-redis来爬，这样可以停机，重新爬，但是单机版有start_requests方法，然而，我的start_url是保存在redis服务器中的，需要从redis接收第一条url那么start_requests方法就不合适。

经过搜索和大佬的经验，重写了make_request_from_data和make_requests_from_url实现了redis中接收start_url。

先看下我使用的机器（树莓派和PC）。发现用了redis之后简直爽歪歪，段时间内不担心重爬等糟心事情。可以断开，树莓派不关机，自动保存items到本地中，爬完之后，我在从reids存到mysql中。

![img](https://pic2.zhimg.com/80/v2-b75bd0093b48bc06d1622221973b96b1_720w.jpg)

## scrapy-redis关键源代码

首先，代码中要继承RedisSpider。

```python
from scrapy_redis.spiders import RedisSpider
class 你的爬虫类(RedisSpider):
    redis_key = "computer:start_urls"
    #...代码并不完整，需要自己添加
    def make_request_from_data(self, data):
        data = json.loads(data)
        url = data.get('url')
        print(url)
        return self.make_requests_from_url(url)
    def make_requests_from_url(self, url):
        '''准备开始爬取首页数据
        '''
         # 第几页，每页30条信息
        page = 1  
        # 根据销量排行爬取
        keyword = ['联想（Lenovo）']
        meta = {"keyword": keyword[0], "page": page}
        req_headers = copy.deepcopy(self.headers)
        req_headers["Referer"] = url
        return scrapy.Request(url, headers=req_headers, callback=self.pagination_parse, 
                 meta=meta,dont_filter=True)
```

看下scrapy-reids中的关键源代码如何实现，冲redis中拿到url。首先，上面继承的RedisSpider也是继承了RedisMixin和Spider两个类。 这次之用到了这个类。

里面就一个方法，具体就看RedisMixin和Spider了。

```python3
class RedisSpider(RedisMixin, Spider):
    @classmethod
    def from_crawler(self, crawler, *args, **kwargs):
        obj = super(RedisSpider, self).from_crawler(crawler, *args, **kwargs)
        obj.setup_redis(crawler)
        return obj
#Spider类
class Spider(object_ref):
    def start_requests(self):
        cls = self.__class__
        if not self.start_urls and hasattr(self, 'start_url'):
            raise AttributeError(
                "Crawling could not start: 'start_urls' not found "
                "or empty (but found 'start_url' attribute instead, "
                "did you miss an 's'?)")
        if method_is_overridden(cls, Spider, 'make_requests_from_url'):
            warnings.warn(
                "Spider.make_requests_from_url method is deprecated; it "
                "won't be called in future Scrapy releases. Please "
                "override Spider.start_requests method instead (see %s.%s)." % (
                    cls.__module__, cls.__name__
                ),
            )
            for url in self.start_urls:
                yield self.make_requests_from_url(url)
        else:
            for url in self.start_urls:
                yield Request(url, dont_filter=True)
    #最后根据这个方法实现你的start_requests,里面是Request，参数等都自己把握即可
    def make_requests_from_url(self, url):
        """ This method is deprecated. """
        return Request(url, dont_filter=True)

#RedisMixin        
class RedisMixin(object):
    def start_requests(self):
        #这里直接返回一个request的方法。返回一批start请求
        """Returns a batch of start requests from redis."""
        return self.next_requests()

    def next_requests(self):
        """Returns a request to be scheduled or none."""
        use_set = self.settings.getbool('REDIS_START_URLS_AS_SET', 
                    defaults.START_URLS_AS_SET)
        fetch_one = self.server.spop if use_set else self.server.lpop
        # XXX: Do we need to use a timeout here?
        found = 0
        # TODO: Use redis pipeline execution.
        while found < self.redis_batch_size:
            #开始关键的地方，redis_key就是外面推进redis服务器的start_url
            data = fetch_one(self.redis_key)
            if not data:
                # Queue empty.
                break
            #关键调用，这里就key重构make_request_from_data，其中data就包含了start_url
            #然后直接返回req了
            req = self.make_request_from_data(data)
            if req:
                yield req
                found += 1
            else:
                self.logger.debug("Request not made from data: %r", data)
        if found:
            self.logger.debug("Read %s requests from '%s'", found, self.redis_key)

    def make_request_from_data(self, data):
        """Returns a Request instance from data coming from Redis.
        By default, ``data`` is an encoded URL. You can override this method to
        provide your own message decoding.
        Parameters
        ----------
        data : bytes
            Message from redis.
        """
        #最后的实现在这里，把start_url放进去就可以了。
        url = bytes_to_str(data, self.redis_encoding)
        return self.make_requests_from_url(url)
```

这里看到最关键就在make_request_from_data和make_requests_from_url的实现上；需要自己重写这两个方法。最后把start_url推到redis中，就可以了。

看下我redis服务器的key；已经准备爬完了。

![img](https://pic1.zhimg.com/80/v2-20a797fbd6c91abcaa35bc4528377ad4_720w.jpg)