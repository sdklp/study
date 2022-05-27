# Windows10安装Redis



# 1. 软件准备

- github下载地址：
- :https://github.com/MicrosoftArchive/redis/releases/tag/win-3.2.100

Redis 支持 32 位和 64 位。这个需要根据你系统平台的实际情况选择，这里我们下载 **Redis-x64-xxx.zip**压缩包

- 官网下载（无WINDOWS版本）：http://redis.io/download



# 2. 安装Redis

- 把下载好的压缩包复制到安装目录，并解压。进入安装目录

![redis目录.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/984499e688444b4b91b8374b33b11438~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

# 3. 运行Redis临时服务

```
cd C:\redis
redis-server.exe redis.windows.conf
复制代码
```

![run redis.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f8db5ffa987c4c2fa8aafd6699238bfa~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

# 4. Redis安装windows服务

```
# 安装服务
redis-server.exe --service-install redis.windows.conf --service-name redisserver --loglevel verbose

# 启动服务
redis-server.exe  --service-start --service-name redisserver

# 停止服务
redis-server.exe  --service-stop --service-name redisserver

# 卸载服务
redis-server.exe  --service-uninstall --service-name redisserver



Win 10下安装 Redis
发布于2020-02-12 18:34:47阅读 8.4K0
目录

写在前面
一、安装环境
二、下载windows版本的Redis
三、安装Redis
四、安装服务
五、启动服务
六、测试Redis
七、常用的Redis服务。
写在前面
Redis 是一个开源使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库。

Redis 通常被称作数据结构数据库，因为值(value) 可以是 字符串(String)、哈希(Hash)、列表(list)、集合(Sets)和有序结合(sorted sets)等类型。

一、安装环境
安装环境：Win 10 家庭版
二、下载windows版本的Redis
官网：http://redis.io/download
github:https://github.com/MicrosoftArchive/redis/releases/tag/win-3.2.100

注意：

官网没有window的版本，需要去github上下载。然后下载压缩包进行安装即可。

三、安装Redis
下载Redis-x64-3.2.100.zip版本，解压到C:/Pulgs/Redis目录
启动cmd 输入命令 redis-server redis.windows.conf。

回车，如图所示：

注意：

可以把Redis的路径添加到系统环境变量里，这样就可以省的再输入路径了，后面的

redis,windows.conf可以省略，如果不省略，会启动默认。

原来的不要关闭，启动另一个cmd窗口。不然无法访问服务端了。

四、安装服务
输入命令：redis-server --service-install redis.windows.conf

回车，如下图所示：

查看在本地的服务。是否成功加入Redis。如图所示表示成功加入了Redis。

五、启动服务
输入命令：redis-server --service-start

如果要停止服务可输入命令：redis-server --service-stop
六、测试Redis
测试输入命令：
redis-cli.exe或
redis-cli.exe -h 127.0.0.1 -p 6379 
set userinfo zjl
get userinfo 
复制

如上图所示表示测试成功。

七、常用的Redis服务。
卸载服务：redis-server --service-uninstall

开启服务：redis-server --service-start

停止服务：redis-server --service-stop
```