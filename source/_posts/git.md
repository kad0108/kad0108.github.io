---
title: Git
date: 2018-04-22 16:58:01
categories: 
- Geek
tags:
- Git
---

## 写在前面

毕业🎓前的日子真是度日如年，厌倦反感学校的一切，只能看书学英语参加各种英语角活动看视频，而且除了跑实验写的那点已经很久很久没有敲代码了。。。稍微学点东西安慰自己。。。之前只是知道Git这些命令怎么用，没有更详细地了解过。看廖老师的博文学习再学一遍Git加深印象。

## Git：分布式版本控制系统

Git自动记录每次文件改动，还可以多人协作编辑，即版本控制，Github则是为开源项目免费提供Git存储。

Git是Linus为了方便管理Linux内核开发而开发的一个源码版本控制软件。

分布式版本控制系统没有中央服务器，比集中式版本控制系统 （例如：SVN）安全性高很多。Git不仅仅是不需要联网这么简单，还有极其强大的分支管理。

安装成功后Git需要自报家门，`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

<!-- more -->

## 创建版本库

版本库又名仓库（repository），可以理解为一个目录，这个目录下的所有文件的改动都可以被Git管理。

```
$ mkdir repository #创建空目录
$ cd repository
$ pwd #显示当前目录
$ git init #将目录变为Git可管理的仓库
$ ls -a #可以查看到该目录下多了一个隐藏.git目录
```

注意⚠️：版本控制系统只能追踪文本文件的改动，比如TXT文件、网页、代码等。而图片、视频这些二进制文件，版本控制系统只能把二进制文件每次改动串起来，只知道图片从100kb改成了120kb，并不知道具体改了什么。

在`repository`目录下新建一个文件，做些改动，将文件添加到版本库的步骤：

```
$ git add <file> #把文件添加到仓库
$ git commit -m "message..." #把文件提交到仓库，命令执行成功后会得到改动信息
```

注意⚠️：可以多次`add`不同文件，然后`commit`一次性提交。

继续修改文件，然后运行`git status`命令可以时刻掌握仓库当前状态。通过命令返回信息可以知道，文件被修改过了，但还没有准备提交的修改。

如果想查看具体做了什么修改，可以使用命令`git diff`。提交修改和提交新文件的步骤是一样的。

![](git-diff.png)

### 小结

初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：

- 第一步，使用命令`git add <file>`，注意，可反复多次使用，添加多个文件；
- 第二步，使用命令`git commit`，完成

修改文件后查看状态：

- 要随时掌握工作区的状态，使用`git status`命令。
- 如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。

## 时光机穿梭

### 版本回退

当不断地修改文件，不断地将修改提交到版本库中，每次`commit`是保存一个快照。如果文件改乱了或者误删了文件，可以从`commit`进行恢复。

可以通过`git log`查看所有的历史改动。想查看简洁版日志可以加上`--pretty=oneline`参数。其中黄色的一大串字符是版本号`commit id`，是由SHA1计算出的十六进制数。

![](git-log.png)

每提交一个新版本，Git会将它们自动串成一条时间线。`HEAD`表示当前版本，`HEAD^`表示上一个版本，`HEAD-100`表示上100个版本。想要把当前版本`add new line`退回到上一个版本`add distributed`，可以使用命令:

```
$ git reset --hard HEAD^
```

可以通过命令`cat readme.txt`查看文件内容是不是版本`add distributed`。

如果这时又想回到最新的版本`add new line`，找到对应的`commit id`就可以回到指定的版本。版本号不需要写全，前几位即可，Git会自动去找。如果找不到版本号，Git提供了命令`git reflog`用来记录你的每一次命令。

![](git-reset.png)

![](git-reflog.png)

Git的版本回退速度很快，因为Git内部有个指向当前版本的`HEAD`指针，当回退版本时，Git只是把指针`HEAD`从`add GPL`改为指向`add distributed`。然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。

> 试了一下如何将本地回滚推送到远端。
`git reset`之后是直接在本地版本库里移动`HEAD`指针到某个`commit`了，所以不需要`add`和`commit`，因为本地工作区没有任何变化。这个时候`git push`是没有用的，想同步更新远端只能强推`git push -f`。建议`reset`在本地操作，强制更新到远端会影响到一起协作开发的小伙伴。
`git revert`只是撤销之前某次`commit`，我认为`revert`并不是真正意义上的回滚，同样`revert`是帮你增加一个`commit`，所以工作区也没有任何变化，不需要`add`和`commit`，直接`push`即可。回滚推送到远端建议采用这种方式。

### 工作区与暂存区

在电脑中能看到的目录，比如`repository`文件夹就是一个**工作区**。

工作区中有个隐藏的目录`.git`是Git的**版本库**。版本库中最重要的就是称为stage的**暂存区**，还有Git自动创建的第一个`master`分支，以及指向`master`的`HEAD`指针。

![](stage.jpeg)

前面提到向Git版本库添加文件时分两步进行，实际上第一步`git add`就是将文件修改添加到暂存区；第二步`git commit`是将暂存区所有内容提交到当前分支。因为创建Git版本库时，Git自动创建唯一一个`master`分支，所以`git commit`是向`master`分支提交更改。可以在每一步操作过程中通过`git status`查看状态，判断暂存区是否clean。

文件修改还在工作区，未`git add`到暂存区时的状态为`Changes not staged for commit`：

![](changes-not-staged.png)

文件修改已经提交到暂存区时的状态为`Changes to be commited`：

![](changes-to-be-commited.png)

**暂存区**是Git非常重要的概念，弄明白了暂存区，就弄明白了Git的很多操作到底干了什么。

### 管理修改

Git之所以比其他版本控制系统设计的优秀，是因为Git跟踪并管理的是**修改**，而非文件。比如操作过程：

```
第一次修改 -> git add -> 第二次修改 -> git commit
```

的结果是第二次修改没有被提交。提交后，用`git diff HEAD -- readme.txt`命令可以查看工作区和版本库里面最新版本的区别。

因为Git管理的是修改，当使用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，`git commit`只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。

想要提交第二次修改：

```
第一次修改 -> git add -> 第二次修改 -> git add -> git commit
```

理解了Git是如何跟踪修改的，每次修改，如果不`add`到暂存区，那就不会加入到`commit`中。

### 撤销修改

想要丢弃工作区的修改可以使用`git checkout -- <file>`。命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令。这个命令是将工作区的全部修改全部撤销，这里有两种情况：

* 一种是文件自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
* 一种是文件已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

即让文件回到最近一次`git commit`或`git add`时的状态。

如果想丢弃的修改已经`git add`到暂存区了，在`commit`之前，你发现了这个问题。用`git status`查看一下，修改只是添加到了暂存区，还没有提交，这时用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区。

`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。

再用`git status`查看一下，现在暂存区是干净的，工作区有修改。想要丢弃工作区的修改继续`git checkout -- <file>`。

### 删除文件

在Git中，删除也是一个修改操作，我们实战一下，先添加一个新文件`test.txt`到Git并且提交。

一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用`rm`命令删了：

```
rm text.txt
```

工作区和版本库就不一致了，`git status`命令会立刻告诉你哪些文件被删除了:

![](rm.png)

根据提示，有两个选择：

* 一是确实要从版本库中删除该文件，那就用命令`git rm <file>`删掉，并且`git commit`，然后文件就从版本库中被删除了。
* 另一种情况是删错了，因为版本库里还有呢，所以`git checkout -- test.txt`可以很轻松地把误删的文件恢复到最新版本。

`git rm <file>`的操作相当于`rm <file> + git add <file>`。

`git checkout --`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

### 小结

版本回退总结：

- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

撤销修改总结：

- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- <file>`。
- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](#版本回退)一节，不过前提是没有推送到远程库。

##  远程仓库

Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。一台电脑上也是可以克隆多个版本库的，只要不在同一个目录下。Github网站是提供Git远程仓库托管服务的。本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，设置如下：

1. 创建SSH Key，`cd ~`到用户主目录下，查看是否有`.ssh`目录，其中有没有`id_rsa`和`id_rsa.pub`这两个文件。如果没有：
```
ssh-keygen -t rsa -C "youremail@example.com"
```

2. 登陆GitHub，打开“Account settings”->“SSH Keys”页面，点“Add SSH Key”，粘贴`id_rsa.pub`文件的内容。

GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。GitHub允许你添加多个Key。只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

### 添加远程库

现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作。

在Github上“create a new repo”，根据提示，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

![](origin-repo.jpeg)

Git默认远程库的名字就是`origin`。把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

推送成功后，可以立刻在GitHub页面中看到远程库的内容已经和本地一模一样。

至此，你拥有了真正的分布式版本库！

### 从远程库克隆

上次讲的是先有本地库，后有远程库的时候，如何关联远程库。下面讲的是，远程库已经准备好了，如何从远程库克隆：

```
git clone <Git URL>
```

也许还注意到，GitHub给出的地址不止一个，还可以用`https://github.com/michaelliao/gitskills.git`这样的地址。实际上，Git支持多种协议，默认的`git://`使用ssh，但也可以使用`https`等其他协议。

使用`https`除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用`ssh`协议而只能用`https`。

![](git-clone.jpeg)

### 小结

添加远程库总结：

* 要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；
* 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；
* 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改，把本地`master`分支的最新修改推送至GitHub；
* 分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！

从远程库克隆总结：

* 要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。
* Git支持多种协议，包括`https`，但通过`ssh`支持的原生`git`协议速度最快。

## 分支管理

分支就是科幻电影里面的平行宇宙。分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

Git的分支与其它版本控制系统相比是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件。

### 创建与合并分支

在[版本回退](#版本回退)一节里提到，每次提交Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即`master`分支。`HEAD`严格来说不是指向提交，而是指向`master`，`master`才是指向提交的，所以，`HEAD`指向的就是当前分支。

一开始的时候，`master`分支是一条线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就能确定当前分支，以及当前分支的提交点：

![](master.png)

当我们创建新的分支，例如`dev`时，Git新建了一个指针叫`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上：

```
$ git checkout -b dev  #-b参数表示创建并切换，相当于以下⬇️两条命令
```

```
$ git branch dev  #新建分支
$ git checkout dev #切换到新分支
```

![](dev.png)

Git创建一个分支很快，因为除了增加一个`dev`指针，改改`HEAD`的指向，工作区的文件都没有任何变化！

可以用`git branch`查看当前分支，命令会列出所有分支，当前分支前面会标有`*`号。

不过，从现在开始，对工作区的修改和提交就是针对`dev`分支了，比如对文件进行修改，新提交一次后，`dev`指针往前移动一步，而`master`指针不变：

![](dev-commit.png)

此时`git checkout master`切换回`master`分支会发现，文件并没有被修改，因为那个提交是在`dev`分支上，而`master`分支此刻的提交点并没有变：

![](checkout-branch.png)

假如我们在`dev`上的工作完成了，就可以把`dev`合并到`master`上。Git怎么合并呢？最简单的方法，就是直接把`master`指向`dev`的当前提交，就完成了合并：

![](merge.png)

所以Git合并分支也很快！就改改指针，工作区内容也不变！

具体做法是，在`master`分支上，把`dev`分支的工作成果合并到`master`分支上：

```
$ git merge dev
Updating 9452784..4ef8073
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

`git merge`命令用于合并指定分支到当前分支。合并后，再查看readme.txt的内容，就可以看到，和`dev`分支的最新提交是完全一样的。

注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

合并完分支后，甚至可以使用命令`git branch -d dev`删除`dev`分支。删除`dev`分支就是把`dev`指针给删掉，删掉后，我们就剩下了一条`master`分支：

![](delete-dev.png)

删除后，查看`branch`，就只剩下`master`分支了。

如果新创建的`dev`分支没有合并就想要删除使用命令`git branch -d dev`会报错并提醒`dev`分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用命令`git branch -D dev`。

因为创建、合并和删除分支非常快，所以Git鼓励使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

### 解决冲突

当不同的分支上的修改均已提交，但是当修改内容有冲突时，合并操作就会有问题。比如`master`分支和`feature1`分支各自都分别有新的提交，就是这样：

![](diff-branch.png)

这时切换到`master`分支进行`git merge feature1`操作将`feature1`分支合并到当前`master`分支时，Git会提示修改存在冲突，必须手动解决冲突再提交。`git status`可以查看冲突文件。

直接查看修改内容：

```
Git is a distributed version control system.
Let's learn git branch.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容。对冲突内容进行修改保存，并再次提交，`master`分支和`feature1`分支变成了：

![](conflict-fixed.png)

用带参数的`git log --graph --pretty=oneline --abbrev-commit`也可以看到分支的合并情况:

![](git-log-merge.png)

> 这里想了解修改冲突内容是在`master`分支上进行的还是同时在`master`和`dev`分支上进行，所以在修改冲突内容后尝试切换分支，结果报error了，提醒文件需要进行合并。使用`git status`查看，提示`You have unmerged paths`。然后继续在当前`master`上做别的修改也还可以，只是在`git add`和`git commit`之前不能切分支，在commit之后`master`和`dev`分支上的内容是一致的。所以暂且认为修改冲突是在`master`分支上进行的，而且修改之后必须对冲突进行操作，可以commit或者abort。

最后删除`feature1`分支`git branch -d feature1`工作完成。

### 分支策略

在实际开发中应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![](branch-team.png)

### Bug分支

每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，想要创建一个分支`issue-101`来修复它，但是当前正在`dev`上进行的工作工作只进行到一半，还没法提交，但是，必须在两个小时内修复该bug，怎么办？

Git还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

```
$ git stash
```

用`git status`查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支。

修复完成后，切换到`master`分支，并完成合并，最后删除`issue-101`分支。

这时再回到`dev`分支继续干活，`git status`查看工作区是干净的，想要查看刚刚保存的工作现场：

```
$ git stash list
stash@{0}: WIP on dev: 96086b0 add test
```

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

1. 用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

2. 用`git stash pop`，恢复的同时把stash内容也删了。

  ```
  $ git stash pop
  On branch dev
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

  	modified:   readme.txt

  no changes added to commit (use "git add" and/or "git commit -a")
  Dropped refs/stash@{0} (52454198962524c309a2f575a2b0ecd1c704487a)
  ```

也可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```
$ git stash apply stash@{0}
```

### 多人协作

当你从远程仓库克隆时，实际上Git自动把本地的`master`分支和远程的`master`分支对应起来了，并且，远程仓库的默认名称是`origin`。

要查看远程库的信息，用`git remote`，或者用`git remote -v`显示更详细的信息，会显示可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到push的地址。

#### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上。

前面[远程仓库](#远程仓库)部分有提到首次推送应该使用`-u`参数，不仅仅是推送，而且将本地`master`分支与远端`master`分支对应起来：

```
$ git push -u origin master
```

如果本地有多个分支，并且切换到另一个`dev`分支上，这时想推送本地`master`分支到远端，应该使用命令：

```
$ git push origin master
```

如果本地有多个分支，而且当前分支也正好是`master`分支，那么可以进一步简化命令为：

```
$ git push
```

如果要推送其他分支道理是一样的，如果在首次没有使用`-u`，那么之后每次推送都需要指明是将本地哪个分支推送到远端，这时如果直接使用`git push`命令会提醒：

```
$ git push
fatal: The current branch dev has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin dev
```

不是所有分支都需要向远端推送，比如bug分支就没必要推送远端。

#### 抓取分支

多人协作时，大家都会往`master`和`dev`分支上推送各自的修改。

模拟多人提交，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆，当从远程库clone时，默认情况下，只能看到本地的`master`分支，可以用`git branch`命令查看。要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是用这个命令创建本地`dev`分支：

```
$ git checkout -b dev origin/dev
```

在`dev`上继续修改，并把修改的`dev`分支`push`到远程。

已经完成模拟他人推送提交，碰巧你也对同样的文件作了修改，并试图推送：

![](git-pull.png)

推送失败，提示远端可能有新的提交和本地提交冲突，解决办法是先`git pull`将最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送。

因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream branch-name origin/branch-name`。

### 小结

创建合并分支总结：

* Git鼓励大量使用分支：
* 查看分支：`git branch`
* 创建分支：`git branch <branch-name>`
* 切换分支：`git checkout <branch-name>`
* 创建+切换分支：`git checkout -b <branch-name>`
* 合并某`<branch-name>`分支到当前分支：`git merge <branch-name>`
* 删除分支：`git branch -d <branch-name>`
* 如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <branch-name>`强行删除。

解决冲突总结：

* 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
* 用`git log --graph`命令可以看到分支合并图。

分支策略总结：

* Git分支十分强大，在团队开发中应该充分应用。
* 合并分支时，加上`--no-ff`参数，比如`git merge --no-ff -m "merge with no-ff" dev`，就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

Bug分支总结：

* 修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
* 当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场。

多人协作总结：

* 查看远程库信息，使用`git remote -v`；
* 本地新建的分支如果不推送到远程，对其他人就是不可见的；
* 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
* 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
* 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
* 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

## 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

Tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起，commit_id则相对比较难记。

> 这里想起来之前在MSRA实习时做的一个工作是重构小英的一个游戏模块，重构完之后上dev和staging分支进行线上测试然后发布，mentor要求我对旧版本和重构后的新版本代码打tag标记，方便出现什么问题时及时取用。

### 创建标签

首先切换到需要打标签的分支上：

```
$ git branch
$ git checkout xxx
```

然后，敲命令`git tag <tag-name>`就可以打一个新标签。

可以用命令`git tag`查看所有标签，这里标签不是按时间顺序列出，而是按字母排序的。

默认标签是打在最新提交的commit上的，如果想要给以前的commit打，方法是通过`git log --pretty=oneline --abbrev-commit`找到历史提交的commit_id，然后打上tag即可：

```
$ git tag <tag-name> <commit_id>
```

可以用`git show <tag-name> `查看标签信息。

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```
$ git tag -a <tag-name> -m <message> <commit_id>
```

还可以通过`-s`用私钥签名一个标签。

### 操作标签

如果标签打错了，也可以删除：

```
$ git tag -d <tag-name>
```

因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

如果要推送某个标签到远程，使用命令`git push origin <tag-name>`。或者一次性推送全部尚未推送到远程的本地标签`git push origin --tags`。

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除，然后，从远程删除：

```
$ git tag -d <tag-name>
$ git push origin :refs/tags/v0.9
```

### 小结

创建标签总结：

- 命令`git tag <tag-name>`用于新建一个标签，默认为`HEAD`，也可以指定一个commit_id；
- 命令`git tag`可以查看所有标签；
- 命令`git show <tag-name>`
- `git tag -a <tag-name> -m "blablabla..."`可以指定标签信息；
- `git tag -s <tag-name> -m "blablabla..."`可以用PGP签名标签。

操作标签总结：

- 命令`git push origin <tag-name> `可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tag-name>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tag-name>`可以删除一个远程标签。

## 使用Github

想要在Github上参与一个开源项目，可以点`Fork`在自己的帐号下clone一个对应的开源项目仓库，然后从自己的帐号下将项目clone到本地，这样自己才能推送修改。自己拥有Fork后的仓库的读写权限。

如果想修改这个开源项目的一个bug，可以本地修改之后推送到自己的仓库中。如果希望开源项目官方可以接受你的修改，可以在GitHub上发起一个pull request。通过推送pull request给官方仓库来贡献代码。

## 自定义Git

### 忽略特殊文件

有些时候，你必须把某些文件放到Git工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件啦，等等，每次`git status`都会显示`Untracked files ...`。

解决办法是在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

不需要从头写`.gitignore`文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：[https://github.com/github/gitignore](https://github.com/github/gitignore)

检验`.gitignore`的标准是`git status`命令是不是说`working directory clean`。

有些时候，你想添加一个文件到Git，但发现添加不了，原因是这个文件被`.gitignore`忽略了，如果你确实想添加该文件，可以用`-f`强制添加到Git：

```
$ git add -f App.class
```

或者你发现，可能是`.gitignore`写得有问题，需要找出来到底哪个规则写错了，可以用`git check-ignore`命令检查:

```
$ git check-ignore -v App.class
```

`.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理！

### 其它

[配置别名－Link](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375234012342f90be1fc4d81446c967bbdc19e7c03d3000)

[搭建Git服务器－Link](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)

[GIT CHEAT SHEET.pdf](git-cheatsheet.pdf)

## 小插曲

因为这次学习Git过程中配置了另一个Github小号的信息，导致在`hexo deploy`的时候报错提示`permission denied`。一开始一直以为是`SSH key`的问题，就重新生成了一遍`SSH key`然后添加到Github，发现并不好使。而且我跑了命令:

```
$ git config user.name
$ git config user.email
$ git remote -v
```

验证信息都是对的，那就奇怪了，怎么可能提交的时候还认为本地是Github小号呢，有想到可能是cache的问题，查了一下然后试了一下，果然。。。

![](git-cache.jpg)




