# Git 使用基础

对于git的学习推荐一下资料：

- GitPro:<http://git-scm.com/book/zh> 很不错的入门书籍
- Git命令在线文档: <http://git-scm.com/docs/> 非常全面
- Git权威指南: <http://book.douban.com/subject/6526452/> 中国人写的比较不错的Git书籍

如果不想敲 git 命令来使用 git，推荐一个非常不错的 git 图形画工具:

<http://www.sourcetreeapp.com/> 各个平台下都有，更新也非常快

要高效快速的使用git,还是建议学习使用git 命令，使用git 也有一段时间了，也准备开始写写技术博客，于是搭建了自己的github博客，记录下自己的学习以及思考，也方便自己查阅。

本文参考：<https://www.atlassian.com/git/tutorials/setting-up-a-repository>

**git init**

`git init` 命令用来创建一个新的git仓库，git 大部分命令都是再git仓库下运行

用法：

```
git init
```

将当前目录转换为 git 仓库

```
git init <directory>
```

将特定目录转换为 git 仓库

```
git init --bare 
```

初始化一个空的（裸的） git 仓库，它不包含工作目录，并且不能提交编辑和提交修改，该仓库主要用于远端服务器管理代码，保存git历史提交的版本信息，防止冲突。如下说明：

比如:A用户在该目录（就称为远端仓库）下执行git操作，且有两个分支(master 和 branchA)，当前在master分支下。B用户想把自己在本地仓库（就称为本地仓库）的master分支的更新提交到远端仓库的master分支，想当然的使用：

```
git push origin master:master
```

于是乎出现问题：因为远端仓库的A用户正在master的分支上操作，而B又要把更新提交到这个master分支上，当然会出现错误(冲突)。但如果是往远端仓库中空闲的分支上提交还是可以的，比如

```
git push origin master:branchB 
```

还是可以成功的, 解决办法就是使用”git init –bare”方法创建一个所谓的裸仓库,也是最好把远端仓库初始化成bare仓库的原因，服务器上部署git 请参考：[服务器上部署 Git](http://git-scm.com/book/zh/服务器上的-Git-在服务器上部署-Git)

例子：

```
cd path/repo
git init
```

**git clone**

```
git clone` 命令用来复制一个已经存在的 git 仓库，相当于 `svn checkout
```

用法：

```
git clone <repo>
```

复制远端仓库 repo 到本地机器

```
git clone <repo> <directory>
```

复制远端仓库 repo directory目录到本地机器

例子：

```
git clone https://example.com/path/project_name.git ~/project/
cd ~/project/project_name
#Start working on the project
```

**git config**

`git config` 命令用来配置本地安装的git

用法：

```
git config user.name <name>
```

配置当前仓库修改提交作者名

```
git config --global user.name <name>
```

通常情况下使用 –global 标记来配置本地所有仓库提交作者名

```
git config --global user.email <email>
```

配置本地所有仓库提交email地址

```
git config --global alias.<alias-name> <git-command>
```

为git命令创建简洁的别名，更多请参考： **让Git命令更简单（Git alias)**

**git add**

`git add` 命令用来跟踪本地修改，为接下来的提交做准备

用法：

```
git add <file>
```

跟踪对文件file的修改

```
git add <directory>
```

跟踪对目录directory下所有修改

```
git add .
```

跟踪本地工作目录下所有修改

例子：

```
git add hello.txt
git commit
```

**git commit**

`git commit`命令用来将本地跟踪的修改添加到本地仓库历史

用法：

```
git commit
```

提交本地修改，启动文本编辑器，编辑提交信息

```
git commit -m"message"
```

提交本地修改，直接编辑提交信息，不启动文本编辑器

```
git commit -a 
```

提交本地工作目录下搜有修改，而不需要先 `git add`,相当于：`git add` 和 `git commit`

```
git commit -a -m"message"
```

同上直接编辑提交信息

**git status**

`git status` 命令用于查看当前工作区和暂存区的状态信息

用法：

```
git status
```

列出暂存过、未暂存过、未跟踪的文件

例子：

```
`# On branch master # Changes to be committed: # (use "git reset HEAD <file>..." to unstage) # #modified: hello.txt # # Changes not staged for commit: # (use "git add <file>..." to update what will be committed) # (use "git checkout -- <file>..." to discard changes in working directory) # #new: test.txt # # Untracked files: # (use "git add <file>..." to include in what will be committed) #`
```