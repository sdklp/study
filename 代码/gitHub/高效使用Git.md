# 高效使用Git

最近给项目组小伙伴讨论了下如何高效使用git，于是在之前几篇基础上＋网络资料整理文档如下，在此记录下：

### 核心概念

谈到Git，最先需要明确的几个概念:

- WorkSpace：工作区，即从仓库中checkout出来的，需要通过Git进行版本控制的目录和文件，可以简单的理解为在文件系统里真实看到的文件
- Stage(Index)：暂存区，或者叫做待提交更新区；在提交进入Repository之前，可以把所有的更新放在暂存区, 用 git add 的文件都在这里
- Repository(Remote/Local)：仓库，一个存放在远端／本地的版本库，用git commit提交的文件就到Local Repository,用git push提交的文件就到Remote Repository
- .git：存放Git管理信息的目录，初始化仓库的时候会自动创建。

有了上面概念的了解，下面就开始上干货，为了方便图解，用xcode创建一个本地TestGit工程来取景

### 创建仓库

这个没什么好说的就简单列下：

`git init` 命令用来创建一个新的git仓库，大部分命令都是在git仓库下运行

`git clone` 命令用来复制一个已经存在的 git 仓库

### 跟踪本地修改（Stage）

`git add` 命令用来跟踪本地修改，为接下来的提交做准备，有以下三种姿势：

`git add <file>` 跟踪单个文件(file)修改

`git add <directory>` 跟踪目录(directory)下所有修改

`git add .` 跟踪当前目录及子目录下所有修改

eg: 创建一个TestView.h .m文件 add 后， git status 后效果如下：

![img](http://strivingboy.github.io/images/git1.png)

上面的`gst` 是 `git status` 的别名，后面说下 `git alias` 接下来就是commit

### commit的几种姿势

`git commit` 提交本地修改，启动文本编辑器，编辑提交信息

`git commit -m"message"` 提交本地修改，直接编辑提交信息，不启动文本编辑器

```
git commit -a`提交本地工作目录下所有修改，而不需要先 `git add`,相当于：`git add` 和 `git commit
```

`git commit -am"message"`同 `git commit -a` 只是不启动文本编辑器

这时提交上面的修改如下：

![img](http://strivingboy.github.io/images/git2.png)

查看log如下：

![img](http://strivingboy.github.io/images/git3.png)

伙计们，有没有发现跟正常的git log 不一样的地方呢^_^, 请看后面 **更好的changelog**

客官别急，还没出大招呢

`git commit --amend -m"message"` 将当前的更改加入上一次commit中并更改最后一次commit的信息。其实观察可发现新的commit是替换了原先的commit，因为commit的hash已经变了。(ps:使用场景：当我们发现上次提交有遗漏的地方，想将修改加入上次提交时 用它是不是很方便^_^)

eg:修改了如下文件，想合并到上次提交

![img](http://strivingboy.github.io/images/git4.png)

再来看提交纪录， 刚才的commit 1 修改为 commit 1 patch

![img](http://strivingboy.github.io/images/git5.png)

OK，接下来献上`git checkout` **重臣**登场

### checkout 技能

之所以将`git checkout` 称为**重臣**，因为它有两类技能：一个是分支相关的操作，另一个是可以恢复文件到之前的某个状态。

- 创建分之和切换分之

```
git checkout －b branch1`创建一个名为”branch1”的分支并切换过去，相当于 `git branch branch1` + `git checkout branch1
```

eg:创建分之 “branch1” 并做一次提交 “commit 2”

![img](http://strivingboy.github.io/images/git6.png)

此刻log如下：

![img](http://strivingboy.github.io/images/git7.png)

- 恢复到之前的某次提交

继续在branch1上做两次提交 “commit 3” 和 “commit 4”, 这时想查看 “commit 2”时代码的样子或作些修改，`git checkout <hash>` 就跑出来帮你了^_^

……在branch1分之上码着代码，小手抖了一下，嚓，上次提交代码有问题，这时，三位小弟蹦出来，`git reset`, `git checkout`, `git revert` 小弟出来到：哥哥别慌，选我、选我……

### reset, checkout, revert竞选

`git reset`、`git checkout`和`git revert`都是用来撤销对代码仓库的各种修改，前两个命令可以操作提交或单个文件，他们如此相似，来各自秀下自己的技能：

`Reset`:命令可以撤销当前分支的某些提交，如：`git reset HEAD^2`当前分支回退了两次提交，`Reset`命令对于想撤销某些提交（前提是当前分支只有自己提交）时非常方便，`Reset`命令有一下标记：

- –soft:使用该标记不会影响暂存区和工作区
- –mixed:使用该标记后，暂存区会更新到了指定的提交，工作区不影响，该标记是默认的
- –hard:暂存区和工作区均更新到指定提交

`git reset --mixed HEAD` 会将本地暂存的修改从暂存区取出来，如果想彻底删除未提交的修改，可以使用`git reset --hard HEAD` （危险：本地修改将不会恢复了哦）`Reset`炫技完毕

`Checkout`:应该非常熟悉了，首先就是切换分支：

`git checkout develop` git 内部会将HEAD游标移动到master分支，并且更新工作区，因为它会重写本地修改，所以git强制我们在`Checkout`之前去提交或暂存工作区中的修改。

`git checkout .` 注意该命令危险！它和 `git reset --hard HEAD` 类似，会彻底删除未提交的修改,不可恢复。可怕…

`Revert`:命令是用一个新的提交来撤销某次历史提交，不会重写提交历史，是安全可恢复的。因此可用于公开提交的分支，这点与`Reset`不同，`Reset`仅用于私有特性分支。虽然我弱小，但是我很专一Y(^_^)Y

……这时 `git reset`胜出：无图无真相啊

![img](http://strivingboy.github.io/images/git8.png)

修改完代码，`git diff`检察官道：看你那么慌，检查一下修改哇，好好好,就敲两个字母嘛 `gd`， `git diff`的别名

![img](http://strivingboy.github.io/images/git9.png)

嚓，小手又抖了，赶紧改去……

特性功能终于码完了，必然和合并到主分之，`git merge` 和 ｀`git rebase`两位大将同时现身

### merge VS rebase

假如现在分支情况如下：

![img](http://strivingboy.github.io/images/01.svg)

**Merge 先来**

最简单的操作就是将branch1分支合并到develop，如下：

```
git checkout feature_branch
git merge master
```

或者

```
git merge master feature_branch
```

最终会在develop上创建一个新的合并提交记录。

`merging` 是一个友好的命令，因为它是一个没有破坏性的操作，现有的分支不会有任何形式的改变，因此也就避免了`rebasing` 一些潜在的缺陷, 相反，这就意味着每一次`merging`都会在develop会遗留一次额外的提交，当develop开的分支非常活跃时，这将导致branch1….N分支的历史记录初步变大，这时查看工程历史记录，就会发现各种分叉，很难理解。

merge后的效果如下：

![img](http://strivingboy.github.io/images/02.svg)

**Rebasing**

作为`merging`的替代方法，我们也可以将branch1分支rebase到develop分支，命令如下：

```
git checkout feature_branch
git rebase master
```

结果将branch1上所有提交移动到了develop分支的最前面，很高效，但与 merge 不同的是 rebase会重写工程提交历史，就像是在develop上重新提交了一遍一样,而不是想merge那样创建一个新的合并提交记录。

`rebasing`最大的优点便是它可以得到一个很清晰的历史记录，首先，它避免了`merging`产生的那个不必要的提交记录，其次，`rebasing`的结果是一个完美的线型提交历史，可以很清楚的看到工程的变化。当然：`rebasing`也存在潜在的问题：1、安全性（在下面的rebase黄金法则中介绍） 2、可追溯性，即：`rebasing`或失去 `merging`创建的合并记录，从而无法查看合并记录点。

merge后的效果如下：

![img](http://strivingboy.github.io/images/03.svg)

**Rebasing黄金法则**

理解了`rebasing`的使用，下来就是何时使用:`The golden rule is to never use it on public branches.`

Example： 假设你讲master 分支rebase 到 feature_brauch分支,结果将master上所有提交移动到feature_brauch的最前面，问题是：这次合并操作只影响feature_brauch分支，所有其他开发这依然在original master分支上工作，由于`rebasing`是重写提交历史，git 会认为所有历史提交已经与其他开发者提交的不同，同步这两个分支唯一的办法是将feature_brauch分支又`merging`到master分支，结果是两次提交集合中大部分包含了相同的修改，不用说,这是一个非常混乱的局面。因此，当执行`rebasing`操作时，时常问下自己：`是不是有其他人在关注改分支？`如果是，则考虑其它方式，否则则可以安全使用`rebasing`。

### 多人多分开发push之三步曲

当多人在同一个分支开发，每次push前都会经历三部： ＋ `git fetch` 从远端将队友提交的代码拉下来 （此时在暂存区） ＋ `git rebase` 和本地代码合并（实际上是将自己的修改重新提交了一遍，至最新提交）， 如果遇到冲突，解决后， git rebase –continue即可 ＋ `git push` 推送本地提交到远端

三步是不是有点繁琐？答案是：YES， `git pull`悍将还没出来呢？

git 默认的 `git pull` 命令相当于 `git fetch` + `git merge` (PS：这也是我们的体检记录无法直视的罪魁祸首) ,怎样将 `git pull` 改为 `git fetch` + `git rebase`呢？ Linus Torvalds 大神当然也会考虑到，使用配置: `git config`

`git config --global branch.autosetuprebase always` 加了 –global 将所有需要合并的操作改为 rebase 而不是 merge ,除非刻意使用 git merge

### 更好的changelog

`git log`一定非常熟悉，如：

git log 命令是查看全部提交日志

git log -2 查看最近2次的提交日志

git log -p 查看历史纪录以来哪几行被修改

git log –oneline 查看历史提交日志，单行显示

如何更好的使用git log来解决使用过程中遇到的需求：（大家有木有遇到呢…）

**提交历史搜索**

```
git log --author="<pattern>"
```

根据提交作者，搜索提交历史 pattern 可以是字符串或这则表达式

```
git log --grep="<pattern>"
```

搜索提交历史 同上pattern 可以是字符串或这则表达式

**更清楚的显示单行提交历史**

`git log --pretty=oneline` 显示如下：

![img](http://strivingboy.github.io/images/git10.png)

如何图形化显示更清晰的提交历史呢？

```
git log --graph --decorate --pretty=oneline --abbrev-commit --all
```

![img](http://strivingboy.github.io/images/git11.png)

能不能再漂亮一点呢？比如：显示提交时间、作者…..当然可以啦

```
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
```

![img](http://strivingboy.github.io/images/git12.png)

漂亮了有木有^_^，每次打完包测试我们都回写下change log以便测试重点去测,格式随意改……

上面的命令这么长，每次敲岂不累死（前提是要记得住，哈哈), git alias轻松搞定 如：

```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

**oh-my-zsh**中git 插件中提供了很多基础git别名, 个人上面使用的配置 (~/.gitconfig)如下：

![img](http://strivingboy.github.io/images/git13.png)

### 常用点补充

- `git stash` 开发中我们经常也会遇到，代码写了一半，要切到另一个分支修复一个bug,或者需要拉取其他小伙伴的代码合并做测试，这时便可以使用 `git stash`经未完成的放在stash 栈中，相关命令有 :

`stash list`:列出stash 所有记录

`stash apply`:将某个纪录取出

`stash clear`:清空stash 栈

……

- `git cherry-pick`

当多人在不同分支开发一个版本时，经常也会遇到 A分之上的小伙伴A debug需要B分之上小伙伴B的某次或多次提交， 这时 `git cherry-pick`朋友值得拥有

### More

`git hooks` 顾名思义 hook, 比如：在push一条commit之后自动给相关伙伴发邮件等,没有实际应用过……