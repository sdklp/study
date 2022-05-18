# git提交时报错:Updates were rejected because the tip of your current branch is behind



![img](https://upload-images.jianshu.io/upload_images/1252625-c35c5979dea51dcf.png?imageMogr2/auto-orient/strip|imageView2/2/w/1142/format/webp)

截图

出现这样的问题是由于:自己当前版本低于远程仓库版本

有如下几种解决方法：

1.使用强制push的方法：

> git push -u origin master -f

这样会使远程修改丢失，一般是不可取的，尤其是多人协作开发的时候。

2.push前先将远程repository修改pull下来

> git pull origin master

> git push -u origin master

3.若不想merge远程和本地修改，可以先创建新的分支：

> git branch [name]

然后push

> git push -u origin [name]