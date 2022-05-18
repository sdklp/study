# wampserver修改项目的目录&添加虚拟域名

一、打开wampserver的安装目录，打开“script”文件夹，用喜欢的编辑器打开里面的config.inc.php.

​    找到“$wwwDir = $c_installDir.'/www';”,把其中的www改为指定目录。（这里修改的只是

​    wampserver上的一个链接）

二、打开Apache中的http.conf文件，按住Ctrl键+F ,查找“DocumentRoot” , 修改后面的路径。

​     再查找“<Directory "C:/www">”, 改www为指定目录。解决！

三、重启。

四、在指定目录下新建test.php写入一个输出“success”的php代码，在浏览器中输入localhost/test.php,

​    如果出现success，说明成功。

​    如果出现这种情况

​    ![img](https://img-blog.csdn.net/20170508203117130?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2Fuc3R5bGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

​    首先查看你的路径有没有错误，如果确定没错的话。

​    将Apache的配置文件httpd.conf中的Include conf/extre/httpd-vhosts.conf改为

​    \#Include conf/extre/httpd-vhosts.conf。

​    之前配置了wampserver的本地虚拟服务器，现在想把本地项目访问从虚拟幻境访问改为

​    http://localhost/这种方式访问，所有导致服务器找不到用户请求的URL。

​    五、重启。

wamp默认是在安装目录的**www**目录下访问项目，但是这样很不方便，我们可以自己指定网站目录，并添加虚拟域名方便输入

想要达到的效果：
我有一个store项目，我不想在wamp安装目录下的www目录下面存放我的项目，我准备把store项目放在D盘的project目录里，不仅是store项目，其他的项目我也准备以后都放在peoject目录里。

![clipboard.png](https://segmentfault.com/img/bVbvV9G)

当我在地址栏输入**store.com**时，就可以访问到我的项目。

## 1.配置httpd.conf文件

### 使httpd-vhosts.conf文件可用

左键点击任务栏中的wamp图标
![clipboard.png](https://segmentfault.com/img/bVbvV6j)

依次选择

```nginx
Apache - httpd.conf
```

打开httpd.conf文件后，搜索下面这行代码

```gradle
Include conf/extra/httpd-vhosts.conf
```

确认该行代码前是否有 **#**，如果添加了#，这行代码则被注释，我们这里需要把#去掉。

### 修改项目路径

ctrl + f 搜索 **documentroot**，修改下面两行代码

```
DocumentRoot "你想修改的项目根路径"
<Directory "你想修改的项目根路径">
```

如，我准备把项目都统一放在D盘的project目录里，我就应该这样写

```
DocumentRoot "D:/project"
<Directory "D:/project/">
```

## 2.配置httpd-vhosts.conf文件

依次选择

```
Apache - httpd-vhosts.conf
```

打开文件后，会发现里面已经添加了一条信息

```
# Virtual Hosts
#
<VirtualHost *:80>
  #设置的虚拟域名
  ServerName localhost 
  #别名
  ServerAlias localhost
  #项目地址 
  DocumentRoot "${INSTALL_DIR}/www" 
  #项目地址 
  <Directory "${INSTALL_DIR}/www/">
    Options +Indexes +Includes +FollowSymLinks +MultiViews
    AllowOverride All
    Require local
  </Directory>
</VirtualHost>
```

意思是当我们在浏览器地址栏输入**localhost**时，会访问到wamp软件安装目录下的www目录。

我们在这段代码下面添加上我们需要添加的其他虚拟域名

```
<VirtualHost *:80>
  ServerName store.com
  DocumentRoot "D:/project/store"
  <Directory "D:/project/store">
    Options +Indexes +Includes +FollowSymLinks +MultiViews
    AllowOverride All
    Allow from all
  </Directory>
</VirtualHost>
```

我设置的虚拟域名是**store.com**，对应的项目目录是D盘的project/store/public作为入口文件（我用的是thinkphp框架，这个框架默认把public里的index.php作为入口文件），当我在地址栏输入store.com的时候，wamp就会去载入D:/project/store/public而不是wamp安装目录下的www目录里的文件。

并修改文件中默认写入的代码

```
# Virtual Hosts
#
<VirtualHost *:80>
  ServerName localhost 
  ServerAlias localhost
  DocumentRoot "D:/project" 
  <Directory "D:/project/">
    Options +Indexes +Includes +FollowSymLinks +MultiViews
    AllowOverride All
    Require local
  </Directory>
</VirtualHost>
```

这样当我们不想给项目设置虚拟域名时，也能通过 **localhost**后面接项目的路径来访问了。

保存并关闭这个文件。

但是这里并没有完，还需要进行其他的设置。

## 配置hosts文件

打开系统的hosts文件
文件路径以windows系统为例（其他操作系统请百度）

```
C:\Windows\System32\drivers\etc
```

打开文件后，在内容的最后添加一条

```
127.0.0.1 store.com
```

添加好后，保存并关闭hosts文件。
最后一步，左键点击任务栏上wamp软件图标 - 重新启动所有服务
完毕！