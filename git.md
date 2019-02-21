---
typora-copy-images-to: ..\Pictures\Typora
---

# Git

[TOC]

## 注意事项

### add

现在，你又理解了Git是如何跟踪修改的，每次修改，如果不`add`到暂存区，那就不会加入到`commit`中。



## 配置config

设置git内容

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址。你也许会担心，如果有人故意冒充别人怎么办？这个不必担心，首先我们相信大家都是善良无知的群众，其次，真的有冒充的也是有办法可查的。

注意`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。 



## 初始化 init


```bash
#set the config using git config
git init
#or
git clone

```





## 提交 commit

```bash
#using the diff option to find the difference between this file to repos
git diff <files>
git show
#after making sure of the changes by diff
git add <files>
git commit
# or you can use git commit -a to commit all files which is added to the repos
# and Git will automatically call the notepad++ and you'll write the message
# Also can be used as git commit -m "this is the version message"
```



##日志 log

```bash
# using git log to inspect the version evolution in this repos
git log
#git log命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是append GPL，上一次是add distributed，最早的一次是wrote a readme file。
#如果嫌输出信息太多，看得眼花缭乱的可以试试加上--pretty=oneline参数则只会显示message和hash version
```



##版本回退 reset



首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`3628164...882e1e0`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本“append GPL”回退到上一个版本“add distributed”，就可以使用`git reset`命令：

```bash
#git reset version

$ git reset --hard HEAD^
HEAD is now at ea34578 add distributed
```



Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`append GPL`：

![git-head](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907584977fc9d4b96c99f4b5f8e448fbd8589d0b2000/0)

改为指向`add distributed`：

![git-head-move](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907594057a873c79f14184b45a1a66b1509f90b7a000/0)

然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。



### 撤销回退 cover to latest version 

撤销回退的办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个`append GPL`的`commit id`是`3628164...`，于是就可以指定回到未来的某个版本：

```
$ git reset --hard 3628164
HEAD is now at 3628164 append GPL
```

现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的`commit id`怎么办？

在Git中，总是有后悔药可以吃的。当你用`$ git reset --hard HEAD^`回退到`add distributed`版本时，再想恢复到`append GPL`，就必须找到`append GPL`的commit id。Git提供了一个命令`git reflog`用来记录你的每一次命令：==引用日志reflog==

```bash
$ git reflog
ea34578 HEAD@{0}: reset: moving to HEAD^
3628164 HEAD@{1}: commit: append GPL
ea34578 HEAD@{2}: commit: add distributed
cb926e7 HEAD@{3}: commit (initial): wrote a readme file
```

终于舒了口气，第二行显示`append GPL`的commit id是`3628164`，现在，你又可以乘坐时光机回到未来了。

### 删除文件

```bash
git rm <filename>
# delete the record of <filename> in repos
```



## **远程仓库**

在[远程仓库](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)一节中，我们讲了远程仓库实际上和本地仓库没啥不同，纯粹为了7x24小时开机并交换大家的修改。

GitHub就是一个免费托管开源代码的远程仓库。但是对于某些视源代码如生命的商业公司来说，既不想公开源代码，又舍不得给GitHub交保护费，那就只能自己搭建一台Git服务器作为私有仓库使用。

搭建Git服务器需要准备一台运行Linux的机器，强烈推荐用Ubuntu或Debian，这样，通过几条简单的`apt`命令就可以完成安装。

假设你已经有`sudo`权限的用户账号，下面，正式开始安装。

第一步，安装`git`：

```bash
$ sudo apt-get install git
```

第二步，创建一个`git`用户，用来运行`git`服务：

```
$ sudo adduser git
```

第三步，创建证书登录：

收集所有需要登录的用户的公钥，就是他们自己的`id_rsa.pub`文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。

**第四步，初始化Git仓库：**

先选定一个目录作为Git仓库，假定是`/srv/sample.git`，在`/srv`目录下输入命令：

**创建的时候要先创建文件夹然后在文件夹内**`git init --bare`

```bash
$ sudo git init --bare sample.git
```

Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以`.git`结尾。然后，把owner改为`git`：

```
$ sudo chown -R git:git sample.git
```

第五步，禁用shell登录：

出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑`/etc/passwd`文件完成。找到类似下面的一行：

```
git:x:1001:1001:,,,:/home/git:/bin/bash
```

改为：

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

这样，`git`用户可以正常通过ssh使用git，但无法登录shell，因为我们为`git`用户指定的`git-shell`每次一登录就自动退出。

第六步，克隆远程仓库：

现在，可以通过`git clone`命令克隆远程仓库了，在各自的电脑上运行：

```
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
```

剩下的推送就简单了。

### 管理公钥

如果团队很小，把每个人的公钥收集起来放到服务器的`/home/git/.ssh/authorized_keys`文件里就是可行的。如果团队有几百号人，就没法这么玩了，这时，可以用[Gitosis](https://github.com/res0nat0r/gitosis)来管理公钥。

这里我们不介绍怎么玩[Gitosis](https://github.com/res0nat0r/gitosis)了，几百号人的团队基本都在500强了，相信找个高水平的Linux管理员问题不大。

### 管理权限

有很多不但视源代码如生命，而且视员工为窃贼的公司，会在版本控制系统里设置一套完善的权限控制，每个人是否有读写权限会精确到每个分支甚至每个目录下。因为Git是为Linux源代码托管而开发的，所以Git也继承了开源社区的精神，不支持权限控制。不过，因为Git支持钩子（hook），所以，可以在服务器端编写一系列脚本来控制提交等操作，达到权限控制的目的。[Gitolite](https://github.com/sitaramc/gitolite)就是这个工具。

这里我们也不介绍[Gitolite](https://github.com/sitaramc/gitolite)了，不要把有限的生命浪费到权限斗争中。

[GitHub远程仓库](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```
$ ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

![1523439473566](C:\Users\ZhangConghai\Pictures\Typora\1523439473566.png)



### 关联remote 

要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

同样的删除方式 `git remote rm origin`



GitHub给出的地址不止一个，还可以用`https://github.com/michaelliao/gitskills.git`这样的地址。实际上，Git支持多种协议，默认的`git://`使用ssh，但也可以使用`https`等其他协议。

使用`https`除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用`ssh`协议而只能用`https`。

要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

Git支持多种协议，包括`https`，但通过`ssh`支持的原生`git`协议速度最快。

### 推送push

关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；

### 拉取Pull

如果本地出现过修改的话pull的时候会出现merge或者overwritten错误

* 提示让用户保护当前更改`commit`或者`stash`缓存更改,pull之后再pop出来.
* 或者使用 `git reset --hard` 重置版本







# Git原理

## 代码回滚：Reset、Checkout、Revert 的选择



Git的库可以分成2到3类

工作区就是实际目录 add是将文件放入stage（暂存区），然后可以用commit上交



![git-repo](C:\Users\ZhangConghai\Pictures\Typora\0.jpg)







![Git repoçä¸»è¦ç»æ](C:\Users\ZhangConghai\Pictures\Typora\68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30312e737667.svg)

### 提交层面的操作

#### Reset

你传给 `git reset` 和 `git checkout` 的参数决定了它们的作用域。如果你没有包含文件路径，这些操作对所有提交生效。我们这一节要探讨的就是提交层面的操作。注意，`git revert` 没有文件层面的操作。

在提交层面上，reset 将一个分支的末端指向另一个提交。这可以用来移除当前分支的一些提交。比如，下面这两条命令让 hotfix 分支向后回退了两个提交。

```
git checkout hotfix
git reset HEAD~2
```

hotfix 分支末端的两个提交现在变成了悬挂提交。也就是说，下次 Git 执行垃圾回收的时候，这两个提交会被删除。换句话说，如果你想扔掉这两个提交，你可以这么做。reset 操作如下图所示：

![把hotfix分支reset到HEAD~2](https://camo.githubusercontent.com/3d55df040cb530482894661b35212b83ff4e5e14/68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30322e737667)

如果你的更改还没有共享给别人，`git reset` 是撤销这些更改的简单方法。当你开发一个功能的时候发现「糟糕，我做了什么？我应该重新来过！」时，reset 就像是 go-to 命令一样。

除了在当前分支上操作，你还可以通过传入这些标记来修改你的缓存区或工作目录：

- --soft – 缓存区和工作目录都不会被改变
- --mixed – 默认选项。缓存区和你指定的提交同步，但工作目录不受影响
- --hard – 缓存区和工作目录都同步到你指定的提交

把这些标记想成定义 `git reset` 操作的作用域就容易理解多了。

把这些标记想成定义 `git reset` 操作的作用域就容易理解多了。

![git rese的定义域](C:\Users\ZhangConghai\Pictures\Typora\68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30332e737667.svg)

这些标记往往和 HEAD 作为参数一起使用。比如，`git reset --mixed HEAD` 将你当前的改动从缓存区中移除，但是这些改动还留在工作目录中。另一方面，如果你想完全舍弃你没有提交的改动，你可以使用 `git reset --hard HEAD`。这是 `git reset` 最常用的两种用法。

当你传入 HEAD 以外的其他提交的时候要格外小心，因为 reset 操作会重写当前分支的历史。正如 rebase 黄金法则所说的，在公共分支上这样做可能会引起严重的后果。

#### Checkout

你应该已经非常熟悉提交层面的 `git checkout`。当传入分支名时，可以切换到那个分支。

```
git checkout hotfix
```

上面这个命令做的不过是将HEAD移到一个新的分支，然后更新工作目录。因为这可能会覆盖本地的修改，Git 强制你提交或者缓存工作目录中的所有更改，不然在 checkout 的时候这些更改都会丢失。和 `git reset` 不一样的是，`git checkout` 没有移动这些分支。

![将 HEAD 从 master 移到 hotfix](C:\Users\ZhangConghai\Pictures\Typora\68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30342e737667.svg)

除了分支之外，你还可以传入提交的引用来 checkout 到任意的提交。这和 checkout 到另一个分支是完全一样的：把 HEAD 移动到特定的提交。比如，下面这个命令会 checkout 到当前提交的祖父提交。

```
git checkout HEAD~2
```

![将 HEAD 移动到任意 commit](C:\Users\ZhangConghai\Pictures\Typora\68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30352e737667.svg)

这对于快速查看项目旧版本来说非常有用。但如果你当前的 HEAD 没有任何分支引用，那么这会造成 HEAD 分离。这是非常危险的，如果你接着添加新的提交，然后切换到别的分支之后就没办法回到之前添加的这些提交。因此，在为分离的 HEAD 添加新的提交的时候你应该创建一个新的分支。

#### Revert

==通过提交之前的内容作为新一版的commit==

Revert 撤销一个提交的同时会创建一个新的提交。这是一个安全的方法，因为它不会重写提交历史。比如，下面的命令会找出倒数第二个提交，然后创建一个新的提交来撤销这些更改，然后把这个提交加入项目中。

```
git checkout hotfix
git revert HEAD~2
```

如下图所示：

![revert到倒数第二个commit](https://camo.githubusercontent.com/ca3c454935277b49e1c75e04644d979e796c50e8/68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30362e737667)

相比 `git reset`，它不会改变现在的提交历史。因此，`git revert` 可以用在公共分支上，`git reset` 应该用在私有分支上。

你也可以把 `git revert` 当作撤销已经提交的更改，而 `git reset HEAD` 用来撤销没有提交的更改。

就像 `git checkout` 一样，`git revert` 也有可能会重写文件。所以，Git 会在你执行 revert 之前要求你提交或者缓存你工作目录中的更改。



### 文件层面的操作

`git reset` 和 `git checkout` 命令也接受文件路径作为参数。这时它的行为就大为不同了。它不会作用于整份提交，参数将它限制于特定文件。

#### Reset

当检测到文件路径时，`git reset` 将缓存区同步到你指定的那个提交。比如，下面这个命令会将倒数第二个提交中的 `foo.py` 加入到缓存区中，供下一个提交使用。

```
git reset HEAD~2 foo.py
```

和提交层面的 `git reset` 一样，通常我们使用HEAD而不是某个特定的提交。运行 `git reset HEAD foo.py` 会将当前的 `foo.py` 从缓存区中移除出去，而不会影响工作目录中对 `foo.py` 的更改。

![将一个文件从 commit 历史中移动到 stage 缓存中](C:\Users\ZhangConghai\Pictures\Typora\68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30372e737667.svg)

`--soft`、`--mixed` 和 `--hard` 对文件层面的 `git reset` 毫无作用，因为缓存区中的文件一定会变化，而工作目录中的文件一定不变。

#### Checkout

Checkout 一个文件和带文件路径 `git reset` 非常像，除了它更改的是工作目录而不是缓存区。不像提交层面的 checkout 命令，它不会移动 HEAD引用，也就是你不会切换到别的分支上去。

![将文件从提交历史移动到工作目录中](C:\Users\ZhangConghai\Pictures\Typora\68747470733a2f2f7777772e61746c61737369616e2e636f6d2f6769742f696d616765732f7475746f7269616c732f616476616e6365642f726573657474696e672d636865636b696e672d6f75742d616e642d726576657274696e672f30382e737667.svg)

比如，下面这个命令将工作目录中的 `foo.py` 同步到了倒数第二个提交中的 `foo.py`。

```
git checkout HEAD~2 foo.py
```

和提交层面相同的是，它可以用来检查项目的旧版本，但作用域被限制到了特定文件。

如果你缓存并且提交了 checkout 的文件，它具备将某个文件回撤到之前版本的效果。注意它撤销了这个文件后面所有的更改，而 `git revert` 命令只撤销某个特定提交的更改。

和 `git reset` 一样，这个命令通常和 HEAD 一起使用。比如 `git checkout HEAD foo.py` 等同于舍弃 `foo.py` 没有缓存的更改。这个行为和 `git reset HEAD --hard` 很像，但只影响特定文件。

### 总结表格

| 命令         | 作用域   | 常用情景                                                   |
| ------------ | -------- | ---------------------------------------------------------- |
| git reset    | 提交层面 | 在私有分支上舍弃一些没有提交的更改                         |
| git reset    | 文件层面 | 将文件从缓存区中移除 —把旧版本添加到缓存区，不改变实际文件 |
| git checkout | 提交层面 | 切换分支或查看旧版本                                       |
| git checkout | 文件层面 | 舍弃工作目录中的更改                                       |
| git revert   | 提交层面 | 在公共分支上回滚更改                                       |
| git revert   | 文件层面 | （然而并没有）                                             |

文件添加到暂存区之后，需要首先将文件从暂存区撤下（unstage）然后才能从HEAD里面checkout 否则取出的内容是暂存区的内容。





## Git的各个状态：

### Untracked

git会监控当前文件夹下的内容，`readme.txt`被修改了，而`LICENSE`还从来没有被添加过，所以它的状态是`Untracked`。

### Unstage

文件没有被放到暂存区里，未上台状态，可以使用commit -a 提交所有相关



























## 何谓分支

为了理解 Git 分支的实现方式，我们需要回顾一下 Git 是如何储存数据的。或许你还记得第一章的内容，Git 保存的不是文件差异或者变化量，而只是一系列文件快照。

在 Git 中提交时，会保存一个提交（commit）对象，该对象包含一个指向暂存内容快照的指针，包含本次提交的作者等相关附属信息，包含零个或多个指向该提交对象的父对象指针：首次提交是没有直接祖先的，普通提交有一个祖先，由两个或多个分支合并产生的提交则有多个祖先。

为直观起见，我们假设在工作目录中有三个文件，准备将它们暂存后提交。暂存操作会对每一个文件计算校验和（即第一章中提到的 SHA-1 哈希字串），然后把当前版本的文件快照保存到 Git 仓库中（Git 使用 blob 类型的对象存储这些快照），并将校验和加入暂存区域：

```
$ git add README test.rb LICENSE
$ git commit -m 'initial commit of my project'
```

当使用 `git commit` 新建一个提交对象前，Git 会先计算每一个子目录（本例中就是项目根目录）的校验和，然后在 Git 仓库中将这些目录保存为树（tree）对象。之后 Git 创建的提交对象，除了包含相关提交信息以外，还包含着指向这个树对象（项目根目录）的指针，如此它就可以在将来需要的时候，重现此次快照的内容了。

现在，Git 仓库中有五个对象：三个表示文件快照内容的 blob 对象；一个记录着目录树内容及其中各个文件对应 blob 对象索引的 tree 对象；以及一个包含指向 tree 对象（根目录）的索引和其他提交信息元数据的 commit 对象。概念上来说，仓库中的各个对象保存的数据和相互关系看起来如图 3-1 所示：

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0301-tn.png)

图 3-1. 单个提交对象在仓库中的数据结构

作些修改后再次提交，那么这次的提交对象会包含一个指向上次提交对象的指针（译注：即下图中的 parent 对象）。两次提交后，仓库历史会变成图 3-2 的样子：

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0302-tn.png)

图 3-2. 多个提交对象之间的链接关系

现在来谈分支。Git 中的分支，其实本质上仅仅是个指向 commit 对象的可变指针。Git 会使用 master 作为分支的默认名字。在若干次提交后，你其实已经有了一个指向最后一次提交对象的 master 分支，它在每次提交的时候都会自动向前移动。

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0303-tn.png)

图 3-3. 分支其实就是从某个提交对象往回看的历史

那么，Git 又是如何创建一个新的分支的呢？答案很简单，创建一个新的分支指针。比如新建一个 testing 分支，可以使用 `git branch` 命令：

```
$ git branch testing
```

这会在当前 commit 对象上新建一个分支指针（见图 3-4）。

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0304-tn.png)

图 3-4. 多个分支指向提交数据的历史

那么，Git 是如何知道你当前在哪个分支上工作的呢？其实答案也很简单，它保存着一个名为 HEAD 的特别指针。请注意它和你熟知的许多其他版本控制系统（比如 Subversion 或 CVS）里的 HEAD 概念大不相同。在 Git 中，它是一个指向你正在工作中的本地分支的指针（译注：将 HEAD 想象为当前分支的别名。）。运行 `git branch` 命令，仅仅是建立了一个新的分支，但不会自动切换到这个分支中去，所以在这个例子中，我们依然还在 master 分支里工作（参考图 3-5）。

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0305-tn.png)

图 3-5. HEAD 指向当前所在的分支

要切换到其他分支，可以执行 `git checkout` 命令。我们现在转换到新建的 testing 分支：

```
$ git checkout testing
```

这样 HEAD 就指向了 testing 分支（见图3-6）。

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0306-tn.png)

图 3-6. HEAD 在你转换分支时指向新的分支

这样的实现方式会给我们带来什么好处呢？好吧，现在不妨再提交一次：

```
$ vim test.rb
$ git commit -a -m 'made a change'
```

图 3-7 展示了提交后的结果。

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0307-tn.png)

图 3-7. 每次提交后 HEAD 随着分支一起向前移动

非常有趣，现在 testing 分支向前移动了一格，而 master 分支仍然指向原先 `git checkout` 时所在的 commit 对象。现在我们回到 master 分支看看：

```
$ git checkout master
```

图 3-8 显示了结果。

![img](https://git-scm.com/figures/18333fig0308-tn.png)

图 3-8. HEAD 在一次 checkout 之后移动到了另一个分支

这条命令做了两件事。它把 HEAD 指针移回到 master 分支，并把工作目录中的文件换成了 master 分支所指向的快照内容。也就是说，现在开始所做的改动，将始于本项目中一个较老的版本。它的主要作用是将 testing 分支里作出的修改暂时取消，这样你就可以向另一个方向进行开发。

我们作些修改后再次提交：

```
$ vim test.rb
$ git commit -a -m 'made other changes'
```

现在我们的项目提交历史产生了分叉（如图 3-9 所示），因为刚才我们创建了一个分支，转换到其中进行了一些工作，然后又回到原来的主分支进行了另外一些工作。这些改变分别孤立在不同的分支里：我们可以在不同分支里反复切换，并在时机成熟时把它们合并到一起。而所有这些工作，仅仅需要 `branch` 和 `checkout` 这两条命令就可以完成。

![img](C:\Users\ZhangConghai\Pictures\Typora\18333fig0309-tn.png)

图 3-9. 不同流向的分支历史

由于 Git 中的分支实际上仅是一个包含所指对象校验和（40 个字符长度 SHA-1 字串）的文件，所以创建和销毁一个分支就变得非常廉价。说白了，新建一个分支就是向一个文件写入 41 个字节（外加一个换行符）那么简单，当然也就很快了。

这和大多数版本控制系统形成了鲜明对比，它们管理分支大多采取备份所有项目文件到特定目录的方式，所以根据项目文件数量和大小不同，可能花费的时间也会有相当大的差别，快则几秒，慢则数分钟。而 Git 的实现与项目复杂度无关，它永远可以在几毫秒的时间内完成分支的创建和切换。同时，因为每次提交时都记录了祖先信息（译注：即 `parent` 对象），将来要合并分支时，寻找恰当的合并基础（译注：即共同祖先）的工作其实已经自然而然地摆在那里了，所以实现起来非常容易。Git 鼓励开发者频繁使用分支，正是因为有着这些特性作保障。

接下来看看，我们为什么应该频繁使用分支。

