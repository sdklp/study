1、创建工程

```
scrapy startproject tencent
```

2、创建项目

```
scrapy genspider mahuateng
```

3、既然保存到数据库，自然要安装pymsql

```
pip install pymysql
```

4、settings文件，配置信息，包括数据库等



```
# -*- coding: utf-8 -*-

# Scrapy settings for tencent project
#
# For simplicity, this file contains only settings considered important or
# commonly used. You can find more settings consulting the documentation:
#
#     https://doc.scrapy.org/en/latest/topics/settings.html
#     https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
#     https://doc.scrapy.org/en/latest/topics/spider-middleware.html

BOT_NAME = 'tencent'

SPIDER_MODULES = ['tencent.spiders']
NEWSPIDER_MODULE = 'tencent.spiders'

LOG_LEVEL="WARNING"
LOG_FILE="./qq.log"
# Crawl responsibly by identifying yourself (and your website) on the user-agent
USER_AGENT = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.131 Safari/537.36'

# Obey robots.txt rules
#ROBOTSTXT_OBEY = True

# Configure maximum concurrent requests performed by Scrapy (default: 16)
#CONCURRENT_REQUESTS = 32

# Configure a delay for requests for the same website (default: 0)
# See https://doc.scrapy.org/en/latest/topics/settings.html#download-delay
# See also autothrottle settings and docs
#DOWNLOAD_DELAY = 3
# The download delay setting will honor only one of:
#CONCURRENT_REQUESTS_PER_DOMAIN = 16
#CONCURRENT_REQUESTS_PER_IP = 16

# Disable cookies (enabled by default)
#COOKIES_ENABLED = False

# Disable Telnet Console (enabled by default)
#TELNETCONSOLE_ENABLED = False

# Override the default request headers:
#DEFAULT_REQUEST_HEADERS = {
#   'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
#   'Accept-Language': 'en',
#}

# Enable or disable spider middlewares
# See https://doc.scrapy.org/en/latest/topics/spider-middleware.html
#SPIDER_MIDDLEWARES = {
#    'tencent.middlewares.TencentSpiderMiddleware': 543,
#}

# Enable or disable downloader middlewares
# See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
#DOWNLOADER_MIDDLEWARES = {
#    'tencent.middlewares.TencentDownloaderMiddleware': 543,
#}

# Enable or disable extensions
# See https://doc.scrapy.org/en/latest/topics/extensions.html
#EXTENSIONS = {
#    'scrapy.extensions.telnet.TelnetConsole': None,
#}

# Configure item pipelines
# See https://doc.scrapy.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'tencent.pipelines.TencentPipeline': 300,
}

# Enable and configure the AutoThrottle extension (disabled by default)
# See https://doc.scrapy.org/en/latest/topics/autothrottle.html
#AUTOTHROTTLE_ENABLED = True
# The initial download delay
#AUTOTHROTTLE_START_DELAY = 5
# The maximum download delay to be set in case of high latencies
#AUTOTHROTTLE_MAX_DELAY = 60
# The average number of requests Scrapy should be sending in parallel to
# each remote server
#AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
# Enable showing throttling stats for every response received:
#AUTOTHROTTLE_DEBUG = False

# Enable and configure HTTP caching (disabled by default)
# See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#httpcache-middleware-settings
#HTTPCACHE_ENABLED = True
#HTTPCACHE_EXPIRATION_SECS = 0
#HTTPCACHE_DIR = 'httpcache'
#HTTPCACHE_IGNORE_HTTP_CODES = []
#HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'



# 连接数据MySQL
# 数据库地址
MYSQL_HOST = 'localhost'
# 数据库用户名:
MYSQL_USER = 'root'
# 数据库密码
MYSQL_PASSWORD = 'yang156122'
# 数据库端口
MYSQL_PORT = 3306
# 数据库名称
MYSQL_DBNAME = 'test'
# 数据库编码
MYSQL_CHARSET = 'utf8'
```



5、items.py文件定义数据字段





```
# -*- coding: utf-8 -*-

# Define here the models for your scraped items
#
# See documentation in:
# https://doc.scrapy.org/en/latest/topics/items.html

import scrapy

class TencentItem(scrapy.Item):
    """
    数据字段定义
    """
    postId = scrapy.Field()
    recruitPostId = scrapy.Field()
    recruitPostName = scrapy.Field()
    countryName = scrapy.Field()
    locationName = scrapy.Field()
    categoryName = scrapy.Field()
    lastUpdateTime = scrapy.Field()

    pass
```



6、mahuateng.py文件主要是抓取数据





```
# -*- coding: utf-8 -*-
import scrapy

import json
import logging
class MahuatengSpider(scrapy.Spider):
    name = 'mahuateng'
    allowed_domains = ['careers.tencent.com']
    start_urls = ['https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1561688387174&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=40003&attrId=&keyword=&pageIndex=1&pageSize=10&language=zh-cn&area=cn']
    pageNum = 1
    def parse(self, response):
        """
        数据获取
        :param response:
        :return:
        """
        content = response.body.decode()
        content = json.loads(content)
        content=content['Data']['Posts']
        #删除空字典
        for con in content:
            #print(con)
            for key in list(con.keys()):
                if not con.get(key):
                    del con[key]
        #记录每一个岗位信息
        # for con in content:
        #     yield con
            #print(type(con))
            yield con
            #logging.warning(con)

        #####翻页######
        self.pageNum = self.pageNum+1
        if self.pageNum<=118:
            next_url = "https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1561688387174&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=40003&attrId=&keyword=&pageIndex="+str(self.pageNum)+"&pageSize=10&language=zh-cn&area=cn"
            yield  scrapy.Request(
                next_url,
                callback=self.parse
            )
```



7、pipelines.py文件主要是对数据进行处理，包括将数据存储到mysql





```
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html

import logging

from pymysql import cursors
from twisted.enterprise import adbapi
import time
# from tencent.settings import MYSQL_HOST
# from tencent.settings import MYSQL_USER
# from tencent.settings import MYSQL_PASSWORD
# from tencent.settings import MYSQL_PORT
# from tencent.settings import MYSQL_DBNAME
#
# from tencent.settings import MYSQL_CHARSET
import copy
class TencentPipeline(object):
    #函数初始化
    def __init__(self,db_pool):
        self.db_pool=db_pool

    @classmethod
    def from_settings(cls,settings):
        """类方法，只加载一次，数据库初始化"""
        db_params = dict(
            host=settings['MYSQL_HOST'],
            user=settings['MYSQL_USER'],
            password=settings['MYSQL_PASSWORD'],
            port=settings['MYSQL_PORT'],
            database=settings['MYSQL_DBNAME'],
            charset=settings['MYSQL_CHARSET'],
            use_unicode=True,
            # 设置游标类型
            cursorclass=cursors.DictCursor
        )
        # 创建连接池
        db_pool = adbapi.ConnectionPool('pymysql', **db_params)
        # 返回一个pipeline对象
        return cls(db_pool)

    def process_item(self, item, spider):
        """
        数据处理
        :param item:
        :param spider:
        :return:
        """
        myItem={}
        myItem["postId"] = item["PostId"]
        myItem["recruitPostId"] = item["RecruitPostId"]
        myItem["recruitPostName"] = item["RecruitPostName"]
        myItem["countryName"] = item["CountryName"]
        myItem["locationName"] = item["LocationName"]
        myItem["categoryName"] = item["CategoryName"]
        myItem["lastUpdateTime"] = item["LastUpdateTime"]
        logging.warning(myItem)
        # 对象拷贝，深拷贝  --- 这里是解决数据重复问题！！！
        asynItem = copy.deepcopy(myItem)

        # 把要执行的sql放入连接池
        query = self.db_pool.runInteraction(self.insert_into, asynItem)

        # 如果sql执行发送错误,自动回调addErrBack()函数
        query.addErrback(self.handle_error, myItem, spider)
        return myItem


    # 处理sql函数
    def insert_into(self, cursor, item):
        # 创建sql语句
        sql = "INSERT INTO tencent (postId,recruitPostId,recruitPostName,countryName,locationName,categoryName,lastUpdateTime) VALUES ('{}','{}','{}','{}','{}','{}','{}')".format(
            item['postId'], item['recruitPostId'], item['recruitPostName'], item['countryName'], item['locationName'],
            item['categoryName'],item['lastUpdateTime'])
        # 执行sql语句
        cursor.execute(sql)
        # 错误函数

    def handle_error(self, failure, item, spider):
        # #输出错误信息
        print("failure", failure)
```



8、创建数据库表



```
Navicat MySQL Data Transfer

Source Server         : 本机
Source Server Version : 50519
Source Host           : localhost:3306
Source Database       : test

Target Server Type    : MYSQL
Target Server Version : 50519
File Encoding         : 65001

Date: 2019-06-28 12:47:06
*/

SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for tencent
-- ----------------------------
DROP TABLE IF EXISTS `tencent`;
CREATE TABLE `tencent` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `postId` varchar(100) DEFAULT NULL,
  `recruitPostId` varchar(100) DEFAULT NULL,
  `recruitPostName` varchar(100) DEFAULT NULL,
  `countryName` varchar(100) DEFAULT NULL,
  `locationName` varchar(100) DEFAULT NULL,
  `categoryName` varchar(100) DEFAULT NULL,
  `lastUpdateTime` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1181 DEFAULT CHARSET=utf8;
```

