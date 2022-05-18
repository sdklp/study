> 学习git的裸库的时候，在网上找了一些教程，都是只说了裸库和正常库的区别，没有给出如何用命令进行实际操作。有的给出了命令，但是也是到处粘贴的，具有跳跃性，不能一步一步的给出操作步骤。

本文就结合git命令如何创建和使用bare裸库给出实际的例子：
规划：

```txt
test-bare.git：是裸库的目录。具体地址E:\JDAZ\ins-ssp-group，注意git命令的时候使用 /e/JDAZ/ins-ssp-group/test-bare.git
test：是第一个git的正常库。主要是做基本常规操作。E:\JDAZ\ins-ssp-group\test
test-bare：是第二个git的正常库。主要是展示从裸库pull数据。即裸库就相当于一个分享库或一个类似中央库的存在，其他人都可以拉取代码。E:\JDAZ\ins-ssp-group\test-bare。这个文件不需要手动创建。

```

##### 1、区别

正常库/普通库：使用`git init`创建即可。包含了工作区，可以正常的进行源文件的编写，提交等各种git常规操作。
裸库：使用`git init --bare`创建。创建之后里面有很多文件，并非网上说了只有一个.git目录。它主要是只保存历史提交的版本信息，不保存文件。作用就是作为分享库。

##### 2、创建裸库

首先可以手动创建裸库的文件夹，命名为test-bare.git。其中git结尾只是一个标识，代表为库。

```shell
cd /e/JDAZ/ins-ssp-group/test-bare.git
git init --bare

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200721103251349.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072110281536.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70)
可以看到裸库中有很多文件

##### 3、创建并使用普通库

手动创建文件夹test

```shell
# 进入test目录中
$ cd /e/JDAZ/ins-ssp-group/test
# init初始化为正常库
qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group/test
$ git init
Initialized empty Git repository in E:/JDAZ/ins-ssp-group/test/.git/
# 编写一个text文件
qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group/test (master)
$ echo 'ss' > test.txt
# 查看text文件
qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group/test (master)
$ cat test.txt
ss
# add操作
$ git add *
warning: LF will be replaced by CRLF in test.txt.
The file will have its original line endings in your working directory.
# commit操作
qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group/test (master)
$ git commit -m 'init'
[master (root-commit) 1275e75] init
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt
# 添加远程仓库，也就是我们的裸库，地址为/e/JDAZ/ins-ssp-group/test-bare.git
qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group/test (master)
$ git remote add origin /e/JDAZ/ins-ssp-group/test-bare.git
# push到裸库中
$ git push -u origin master
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 203 bytes | 203.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To E:/JDAZ/ins-ssp-group/test-bare.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.

```

至此，普通库test，可以提交文件并且上传push到裸库中。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072110403346.png)

##### 4、查看裸库

现在去查看裸库中的信息，看是不是有记录push上来了。

```shell
$ cd /e/JDAZ/ins-ssp-group/test-bare.git

qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group/test-bare.git (BARE:master)
$ git log
commit 905ebbae2949fb111c714c0378390b9a6067eb84 (HEAD -> master)
Author: qsm<qsm@xx.com>
Date:   Tue Jul 21 10:38:53 2020 +0800

    init

```

我们可以看到裸库中确实有信息存在

##### 5、裸库中拉取数据

我们可以看到裸库中确实有提交的记录，那么我们看能否拉取数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200721104415700.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70)
直接在ins-ssp-group目录下操作

```shell
qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group
$ cd /e/JDAZ/ins-ssp-group

qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group
$ git clone /e/JDAZ/ins-ssp-group/test-bare.git
Cloning into 'test-bare'...
done.


```

可以看到确实生成了test-bare库，里面也存在了text文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200721104613636.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200721104620551.png)

##### 6、裸库push到远程仓库

将裸库中的信息保存到gitlab的远程仓库
首先在git得建立一个项目
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200721105133990.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70)
地址为：http://git.xxx.com/ins-ssp-group/baretest

然后在本地的裸库中操作

```shell
$ cd /e/JDAZ/ins-ssp-group/test-bare.git
qsm@ZB-PF1EJ7LN MINGW64 /e/JDAZ/ins-ssp-group/test-bare.git (BARE:master)
$ git push --mirror http://git.xxx.com/ins-ssp-group/baretest
warning: redirecting to http://git.xxx.com/ins-ssp-group/baretest.git/
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 199 bytes | 199.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To http://git.xxx.com/ins-ssp-group/baretest
 * [new branch]      master -> master


```

查看远程仓库
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200721105243343.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70)

至此git的裸库bare操作到此结束。若是安装步骤一步一步来，应该明白了裸库的含义和用法了。