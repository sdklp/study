### 0.前言

一般情况下同步代码可以通过在GitHub/GitLab等网站新建远程仓库，所有机器都向仓库推送或者从仓库下拉更新。

上述过程步骤也不算复杂，不过有时候我们考虑到仓库的安全性等因素，只想在局域网内共享仓库，并且允许局域网中指定的机器推送或下拉更新。

这就是本文试图记录的操作过程的背景。

### 1.新建中转仓库

中转仓库其实是一个裸仓库，这个仓库文件夹里只有.git里的版本信息，没有代码。
所有工作者都只与中转仓库建立联系，这样冲突只会发生在中转仓库，各机本地代码不会冲突，从而最大程度上避免混乱。

```Bash
mkdir myrepo.git && cd myrepo.git  //创建myrepo.get目录，并进入这个目录

git init --bare --shared

cv:myrepo.git cv$ git remote add origin file:///Users/cv/misc_codes/myrepo.git

cv:myrepo.git cv$ git remote
origin
```

显示结果为origin，表示我们操作成功且已经生效。

然后注意要将中转仓库的路径设置为局域网共享状态。macOS系统下，“系统偏好设置”——>“共享”——>“文件共享”复选框——>“共享文件夹”添加仓库所在路径。

### 2.构建本机克隆仓库

在设置中转仓库的机器上新建克隆仓库，可以修改代码并上传。

```Bash
cv:misc_codes cv$ git clone file:///Users/cv/misc_codes/myrepo.git mylocalrepo_a.git

cv:misc_codes cv$ cd mylocalrepo_a.git

cv:mylocalrepo_a.git cv$ cat > README
Hello world!
```

修改之后保存并提交。

```Bash
cv:mylocalrepo_a.git cv$ git add .
cv:mylocalrepo_a.git cv$ git commit -m "Init the test repo"
cv:mylocalrepo_a.git cv$ git branch --unset-upstream
cv:mylocalrepo_a.git cv$ git push -u origin --all
```

### 3.在其他机器同步仓库

在另外的机器上新建克隆仓库，通过ssh建立仓库之间的连接。可以用于拉取和上传更新。

通过ssh的方式需要知道中转仓库所在机器的用户名和IP地址，基本格式为`git clone ssh://username@ipaddr/path/to/repo.git localrepo.git`。主要步骤展示如下。

```Bash
cvxy:misc_codes cvxy$ git clone ssh://cv@192.168.1.15/Users/cv/misc_codes/myrepo.git mylocalrepo_b.git

cvxy:misc_codes cvxy$ cd mylocalrepo_b.git

cvxy:mylocalrepo_b.git cvxy$ git pull origin master

cvxy:mylocalrepo_b.git cvxy$ cat >> README
Great idea.
```

保存修改并推送到中转仓库。

```Bash
cvxy:mylocalrepo_b.git cvxy$ git add .
cvxy:mylocalrepo_b.git cvxy$ git commit -m "Modification from machine b"
cvxy:mylocalrepo_b.git cvxy$ git push origin master
```

至此，在不创建远程仓库的前提下可以实现在不同机器之间的同步更新。