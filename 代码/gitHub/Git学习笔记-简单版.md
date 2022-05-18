#### Git

------

##### 基础

1. 分布式**版本控制**系统；SVN都是集中式的版本控制系统。有无“中央服务器”。

2. GitHub，全球管理代码最大的网址，提供git服务。

3. 安装教程

   ```
   1.下载安装包，选择安装路径，默认组件
   2.安装成功，命令或者右键
   3.设置用户和邮箱
   	$ git config --global user.name "Your Name"
   	$ git config --global user.email "email@example.com"
   4.创建仓库，也即是一个文件夹，进入，git init 初始化该文件夹为git仓库
   
   1234567
   ```

##### 2、Git使用

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518112622270.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70#pic_center)

1. 提交命令，工作区，暂存区，本地仓库（分支）。三者可以统称为工作目录（区别于裸库）；其中暂存区和本地仓库可称为版本库，其信息存在于.git文件夹中。
   更多关于裸库的详情可以参考Git小技巧——git的bare裸库命令操作——一看就会

   ```
   1.git add
   2.git commit -m "xxxx"  注意git commit -am "xxxx" 用于省略add，但是必须是之前被追踪（add过）
   3.git status
   -----
   4.git push   (github 若403，则未授权，.git文件夹中config文件可以添加账号密码）
   5.git pull 拉最新版本
   -----
   添加远程分支 git remote add origin/upstream git地址
   git中如何将本地项目和两个远程仓库连接（git remote add <shortname> <url> 添加一个新的远程 Git 仓库）
   123456789
   ```

2. 回退命令

   ```
   1.查看版本，也就是commit编号：git log --pretty=oneline
   2.回退到某个版本，git reset --hard/mixed(默认)/soft commit id
   3.回到未来，退回到某个版本之后，又想回到刚刚最新的版本，使用git reflog查看刚刚最新的commit ID号，再使用reset
   
   1、git log                    打印版本日志
   2、git status                   查看当前分支
   3、git reset --hard 版本号                      复制需要回滚到的版本号，然后运行此命令
   4、git push --force                        将本地的当前版本强制提交到远程仓库中
   
   有记录
   git revert 
   
   head
   head^和head~
   “^”代表父提交,当一个提交有多个父提交时，可以通过在”^”后面跟上一个数字，表示第几个父提交，”^”相当于”^1”
    ~<n>相当于连续的<n>个”^”.
    https://blog.csdn.net/sayoko06/article/details/79471173
   1234567891011121314151617
   ```

   关于reset和revert的详情可以参考Git小技巧——撤销&回滚操作revert和reset
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518113023208.png#pic_center)

3. 远程仓库

   可以创建project仓库，也可以再上一层创建group

   ```
   1.Https
   	需要账号和密码（本机？公司？Git分配？）
   	
   2.SSH，仅仅是鉴定身份的方式不同
   	安装OpenSSL,生成公钥和私钥，github上配置公钥即可。
   	sh-keygen -t rsa -C "xxx@jd.com"
   		第一个目录,直接回车，默认.ssh目录。
   		第二个和第三个设置私钥的保护密码
   		进入.ssh目录，打开id_rsa.pub，复制里面的key
   123456789
   ```

   上传本地项目到git远程仓库，可以参考本地项目上传git远程仓库

4. 仓库命令

   ```
   分支管理
   	1.查看分支：git branch 	-a		
   	2.创建分支：git branch 分支名
   	3.删除分支：git branch -d 分支名
   	4.切换分支：git checkout 分支名
   	5.创建并切换分支：git checkout -b 分支名
   	5.合并分支：git merge 被合并的分支名。分支的merge操作
   冲突管理
   	冲突的原因是，本地版本与线上版本不一致。一般非冲突的文件自动合并，若是都共同修改了某个文件则需要手	动处理冲突。
   	
   12345678910
   ```

5. stash命令，stash能够将所有未提交的修改（工作区和暂存区）保存至堆栈中，用于后续恢复当前工作目录。

   ```
   git stash list所有历史的stash
   
   stash若是有急需bug修复
   	1.可以把自己未处理完的事情，提交commit一下，然后切换分支到hotfix分支。
   	2.也可以把自己未处理完的事情，先git stash一下存在堆栈区，然户切换到hotfix分支，处理完bug之后。返回原来分支，然后git stash pop弹出。弹出可能会冲突，可以把自己修改备份，接受别人的。
   	
   	git stash apply :应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即stash@{0}，如果要使用其他个，git stash apply stash@{$num} ， 比如第二个：git stash apply stash@{1} 
   	git stash pop ：命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1}
   12345678
   ```

6. 忽略

   ```
   创建.gitignore文件
   忽略某些目录或者文件，不上传到git
   ！则是取反，要上传某些目录和文件
   
   !.mvn/wrapper/maven-wrapper.jar
   !**/src/main/**
   !**/src/test/**
   
   /jd/
   help.txt
   .classpath
   1234567891011
   ```

7. rebase和merge.参考

   ```
   git pull = git fetch + git merge
   git pull --rebase = git fetch + git rebase  ;一条直线
   
   1、git fetch：相当于是从远程获取最新版本到本地（本地的head没有改变），不会自动merge（即合并为新的commit，head指向最新的commit）。在实际使用中，git fetch更安全一些。因为在merge前，我们可以查看更新情况，然后再决定是否合并。
   2、git pull：相当于是从远程获取最新版本并merge到本地，相当于git fetch和git merge。
   3、git rebase：主要用在从上游分支获取最新commit信息，并有机的将当前分支和上游分支进行合并。和git merge效果一样，原理不同，git rebase过程相比较git merge合并整合得到的结果是一样的，但是通过git rebase衍合能产生一个更为整洁的提交历史。
   123456
   ```

   在master分支上使用 git merge develop
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518113435579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70)
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518113507322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70)
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020051811353385.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70)

8. head与分支：参考

   ```
   HEAD：当前 commit 的直接或者间接引用(最新)，
   
   HEAD 是指向当前 commit 的引用，它具有唯一性，每个仓库中只有一个 HEAD 。在每次提交 时它都会自动向前移动到最新的 commit 。
   branch分支是一类引用。HEAD除了直接指向commit也可以通过指向某个branch来间接指向commit。当HEAD指向一个branch时，commit发生时，HEAD会带着它所指向的branch一起移动。
   1234
   ```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518113617816.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM1NDE3MDc=,size_16,color_FFFFFF,t_70#pic_center)