# 一、创建版本库
1、初始化一个Git仓库，使用git init命令。

```
$ git init
```
2、用命令**git add**告诉Git，把文件添加到仓库：（等同于vscode中的**暂存更改**）

```
$ git add readme.txt  
```
3、用命令**git commit**告诉Git，把文件提交到仓库：（ 等同于vscode中的**提交**）

```
$ git commit -m "wrote a readme file" 
//-m后面输入的是本次提交的说明，可以输入任意内容，
//当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+) 
//1个文件被改动（我们新添加的readme.txt文件）；2 insertions：插入了两行内容（readme.txt有两行内容）。
 create mode 100644 readme.txt
```
# 二、时光穿梭机
## git status

**等同于vscode的更改列表**

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
**git status**命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们readme.txt被修改过了，但还没有准备提交的修改。

## git diff

**在vscode中，单击更改列表中的任意一项，可以实现相同效果**

查看对readme.txt作了什么修改，再把它提交到仓库就放心多了，提交修改和提交新文件是一样的两步，第一步是**git add**，第二步**git commit -m "add distributed"**
## 版本回退
**每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为commit。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。**

### git log
**版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用git log命令查看：**

```
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) 
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```
**git log**命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是append GPL，上一次是add distributed，最早的一次是wrote a readme file。

### git reset
现在我们启动时光穿梭机，准备把readme.txt回退到上一个版本，也就是add distributed的那个版本，怎么做呢？

首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本。上一个版本就是HEAD^， 上上一个版本就是HEAD^^， 当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

* 我们要把当前版本append GPL回退到上一个版本add distributed，就可以使用**git reset**命令：
```
$ git reset --hard HEAD^ //在Windows中要给^加双引号 =》 git reset --hard HEAD"^"
HEAD is now at e475afc add distributed //回退版本后，当前版本号
```
* 当我们后悔之前的操作，想要回到最新的版本时，只要上面的命令行窗口还没有被关掉，可以使用：
```
git reset --hard 1094a 
//1094a为版本号（版本号没必要写全，前几位就可以了，Git会自动去找。），
//回到1094a版本
```
* 当我们后悔之前的操作，想要回到最新的版本时，并且上面的命令行窗口已经被关掉了，Git提供了一个命令**git reflog**用来记录你的每一次命令：
```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```
终于舒了口气，从输出可知，append GPL的commit id是1094adb，现在，你又可以乘坐时光机回到未来了。

> 退出git log状态：按下英文状态的Q

## 工作区和暂存区
### 工作区

Working Directory

就是你在电脑里能看到的目录，比如我的learngit文件夹就是一个工作区：
![image.png](https://upload-images.jianshu.io/upload_images/16269351-90c20e4c2cac77c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 版本库

Repository

工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。
Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。
![image.png](https://upload-images.jianshu.io/upload_images/16269351-b220b5a62a450cb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：

* 第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；

* 第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
## 撤销修改
### git checkout -- filename
可以丢弃工作区的修改(**等同于vscode的放弃更改**)
```
git checkout -- readme.txt
```
### git reset HEAD <filename>
可以把暂存区的修改撤销掉（unstage），重新放回工作区：（**等同于vscode取消暂存更改**）
```
$ git reset HEAD readme.txt
Unstaged changes after reset:
M	readme.txt
```
## 删除文件

一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用`rm`命令删了：

```
$ rm test.txt
```

这个时候，Git知道你删除了文件，因此，工作区和版本库就不一致了，`git status`命令会立刻告诉你哪些文件被删除了：

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

**现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit：**

```
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```
现在，文件就从版本库中被删除了。

**删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：**

```
$ git checkout -- test.txt
```
# 三、远程仓库

## SSH加密

由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

* 第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
```
$ ssh-keygen -t rsa -C "youremail@example.com"
```
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
* 第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容：
## 添加远程仓库

### `git remote add name`

在本地的learngit仓库下运行命令：
```
$ git remote add origin 仓库地址 
 //添加后，远程库的名字就是origin，这是Git默认的叫法，
//也可以改成别的，但是origin这个名字一看就知道是远程库。
```
## 拉取远程仓库的初始文件

### git pull

```bash
git pull --rebase origin master
```

## 推送内容到远程库

### git push

可以把本地库的所有内容推送到远程库上：（等同于vscode的推送）

```
$ git push -u origin master 
//由于远程库是空的，我们第一次推送master分支时，加上了-u参数，
//Git不但会把本地的master分支内容推送的远程新的master分支，
//还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

Counting objects: 20, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (20/20), 1.64 KiB | 560.00 KiB/s, done.
Total 20 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), done.
To github.com:michaelliao/learngit.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。推送成功后，可以立刻在GitHub页面中看到远程库的内容已经和本地一模一样
##4、从现在起，只要本地作了提交，就可以通过命令：
```
$ git push origin master
```
>要克隆一个仓库，首先必须知道仓库的地址，然后使用git clone命令克隆。
>```git
>$ git clone 仓库地址
>```
# 四、分支管理

## git branch

## git checkout

**1、我们创建dev分支，然后切换到dev分支：**

```
$ git checkout -b dev
Switched to a new branch 'dev'
```
**git checkout**命令加上-b参数表示创建并切换，相当于以下两条命令：

```
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```
然后，用**git branch**命令查看当前分支：
```
$ git branch
* dev
  master
```
>在分支上工作时，操作与在master上一致。当我们在分支dev上工作结束之后，可以将它合并到master上

**2、dev分支的工作完成，我们就可以切换回master分支：**

```
$ git checkout master
Switched to branch 'master'
```
## git merge

**3、现在，我们把dev分支的工作成果合并到master分支上：**

```
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```
**4、合并完成后，就可以放心地删除dev分支了：**

```
$ git branch -d dev
Deleted branch dev (was b17d20e).
```
删除后，查看branch，就只剩下master分支了：
```
$ git branch
* master
```
>因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全。
---
## switch
我们注意到切换分支使用**git checkout <branch>**，而前面讲过的撤销修改则是**git checkout -- <file>**，同一个命令，有两种作用，确实有点令人迷惑。

实际上，切换分支这个动作，用switch更科学。因此，最新版本的Git提供了新的git switch命令来切换分支：

创建并切换到新的dev分支，可以使用：
```
$ git switch -c dev
```
直接切换到已有的master分支，可以使用：
```
$ git switch master
```
使用新的**git switch**命令，比git checkout要更容易理解。
#五、解决冲突
当我们在dev分支和master分支上同时对一个文件作了修改，并进行了提交

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，我们试试看：
```
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```
果然冲突了！Git告诉我们，readme.txt文件存在冲突，必须手动解决冲突后再提交。我们可以直接查看readme.txt的内容：
```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```
Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容.
将两个分支内容修改为一样，即可解决冲突。
# 六、Bug分支
软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

## git stash

1. **当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支issue-101来修复它，但是，等等，当前正在dev上进行的工作还没有提交：**
2. **并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug，怎么办？**
3. **幸好，Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：（等同于vscode的储藏）**

```
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```
现在，用**`git status`**查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复`bug`。

   4.**当bug修复完毕之后，是时候接着回到dev分支干活了！**

```
$ git checkout dev
Switched to branch 'dev'

$ git status
On branch dev
nothing to commit, working tree clean
```
工作区是干净的，刚才的工作现场存到哪去了？用**`git stash list`**命令看看：
```
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```
  5.**工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：（等同于vscode的弹出储藏）**

* 一是用**`git stash apply`**恢复，但是恢复后，stash内容并不删除，你需要用**`git stash drop`**来删除；

* 另一种方式是用**`git stash pop`**，恢复的同时把stash内容也删了：
```
$ git stash pop
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
```
再用**`git stash list`**查看，就看不到任何stash内容了：
```
$ git stash list
```
你可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：
```
$ git stash apply stash@{0}
```
# 七、Feature分支
**开发一个新feature，最好新建一个分支；**

**如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。**

# 八、多人协作
## git remote
当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。

要查看远程库的信息，用git remote：
```
$ git remote
origin
```
或者，用git remote -v显示更详细的信息：
```
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```
上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。

##  git push

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```
$ git push origin master
```
如果要推送其他分支，比如dev，就改成：
```
$ git push origin dev
```
但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

* master分支是主分支，因此要时刻与远程同步；

* dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；

* bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；

* feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！
## 抓取分支
**1、当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支。不信可以用git branch命令看看：**

```
$ git branch
* master
```
**2、要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：**

```
$ git checkout -b dev origin/dev
```
**3、现在，他就可以在dev上继续修改，然后，时不时地把dev分支push到远程：**

**4、你的小伙伴已经向origin/dev分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送，会提示推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用git pull（等同于vscode的拉取）把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送：**

**5、git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：**

```
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```
再pull
>**因此，多人协作的工作模式通常是这样：**

* 首先，可以试图用**git push origin <branch-name>**推送自己的修改；

* 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

* 如果合并有冲突，则解决冲突，并在本地提交；

* 没有冲突或者解决掉冲突后，再用**git push origin <branch-name>**推送就能成功！

* 如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令**git branch --set-upstream-to <branch-name> origin/<branch-name>**。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。
# 九、标签管理
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

# 十、commit 规范

它的 `message` 格式如下:

```html
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
复制代码
```

分别对应 Commit message 的三个部分：`Header`，`Body` 和 `Footer`。

## Header

`Header` 部分只有一行，包括三个字段：`type`（必需）、`scope`（可选）和`subject`（必需）。

* `type`: 用于说明 commit 的类型。一般有以下几种:

```bash
feat: 新增feature
fix: 修复bug
docs: 仅仅修改了文档，如readme.md
style: 仅仅是对格式进行修改，如逗号、缩进、空格等。不改变代码逻辑。
refactor: 代码重构，没有新增功能或修复bug
perf: 优化相关，如提升性能、用户体验等。
test: 测试用例，包括单元测试、集成测试。
chore: 改变构建流程、或者增加依赖库、工具等。
revert: 版本回滚
```

* `scope`: 用于说明 commit 影响的范围，比如: views, component, utils, test...

* `subject`: commit 目的的简短描述

## Body

对本次 commit 修改内容的具体描述, 可以分为多行。如下所示:

```bash
# body: 72-character wrapped. This should answer:
# * Why was this change necessary?
# * How does it address the problem?
# * Are there any side effects?
# initial commit
```

## Footer

一些备注, 通常是 `BREAKING CHANGE`(当前代码与上一个版本不兼容) 或修复的 bug(关闭 Issue) 的链接。

简单介绍完上面的规范，我们下面来说一下`commit.template`，也就是 git 提交信息模板。

[commit规范](https://juejin.im/post/5e0c82a15188253a907111dc)

# 十一、git rest

## 参数

###  --soft  

仅仅移动当前Head指针，不会改变工作区和暂存区的内容  
![img](https://images0.cnblogs.com/blog2015/737565/201505/182237338854646.png)

### --mixed  

是reset的默认参数,移动head指针，改变暂存区内容，但不会改变工作区 
![img](https://images0.cnblogs.com/blog2015/737565/201505/182238202609380.png)

### --hard  

当前head指针、工作区和暂存区内容全部改变  
![img](https://images0.cnblogs.com/blog2015/737565/201505/182238305101646.png)

## 如何回滚文件

### 修改完，还未执行git add 

**vscode中的放弃更改操作**

```bash
$ git checkout filename
```

使用暂存区的文件覆盖工作区，所以执行完`git add filename`之后，再执行该命令是无效的  

`git checkout filename和git add filename`是一对反义词

### 使用git add 提交到暂存区，还未commit之前

**vscode中的取消暂存更改操作**

```bash
$ git reset HEAD filename
```

### 已经git commit，还未git push

**vscode中的撤销上次提交**

**版本控制系统肯定有某个命令可以告诉我们commit历史记录，在Git中，我们用git log命令查看：**

```
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) 
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

**git log**命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是append GPL，上一次是add distributed，最早的一次是wrote a readme file。

首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本。上一个版本就是HEAD^， 上上一个版本就是HEAD^^， 当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

* 我们要把当前版本append GPL回退到上一个版本add distributed，就可以使用**git reset**命令：

```
$ git reset --hard HEAD^ //在Windows中要给^加双引号 =》 git reset --hard HEAD"^"
HEAD is now at e475afc add distributed //回退版本后，当前版本号
```

* 当我们后悔之前的操作，想要回到最新的版本时，只要上面的命令行窗口还没有被关掉，可以使用：

```
git reset --hard 1094a 
//1094a为版本号（版本号没必要写全，前几位就可以了，Git会自动去找。），
//回到1094a版本
```

* 当我们后悔之前的操作，想要回到最新的版本时，并且上面的命令行窗口已经被关掉了，Git提供了一个命令**git reflog**用来记录你的每一次命令：

```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

终于舒了口气，从输出可知，append GPL的commit id是1094adb，现在，你又可以乘坐时光机回到未来了。

### 已经git push  

首先，通过`git log`查看提交信息，以便获取需要回退至的版本号：

```
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) 
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

执行 git reset –-soft <版本号> ，如回退到add distributed版本

```
$ git reset --soft e475afc93c209a690c39c13a46716e8fa000c366
```

重置至指定版本的提交，达到撤销提交的目的

> 参数**soft**指的是：保留当前工作区，以便重新提交
> 还可以选择参数**hard**，会撤销相应工作区的修改，一定要谨慎使用

**然后，通过`git log`确认是否成功撤销：**

```
$ git log
commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

此时，已重置至指定版本的提交，log中已经没有了需要撤销的提交

再执行 **git push origin 分支名 –-force** ，强制提交当前版本号，以达到撤销版本号的目的：

```
$ git push origin master --force
Total 0 (delta 0), reused 0 (delta 0)
To github.com:hanchao5272/myreflect.git
 + a448220...aa909cf master -> master (forced update)
```

必须添加参数**force**进行强制提交，否则会提交失败，并报错：

报错原因：**本地项目版本号低于远端仓库版本号**。

**至此撤销push提交完成**

后续如果有修改代码，则执行正常提交操作即可

```
修改代码，重新提交和推送
//修改代码，添加修改
git add filename
//重新提交
git commit -m "hello, world"
//重新推送
git push origin master
```

# 十二、Git相关术语

1)、仓库（Repository）
保存了所有文件的修改历史。

2)、工作区（Working Directory)
本地电脑上的工作目录文件夹。

3)、暂存区（Staging area）
暂存区是暂存工作区的文件变化。

4)、索引（Index）
索引是暂存区的另一种术语，即暂存区的别名。

5)、签入（Checkin）
将新版本复制回仓库

6)、签出（Checkout）
从仓库中将文件的最新修订版本复制到工作区

7)、提交（Commit）
将暂存区的文件提交到仓库

8)、冲突（Conflict）
多人对同一文件的同一部分进行了修改，导致了冲突的发生。

9)、合并（Merge）
将指定分支合并到当前分支

10)、分支（Branch）
从当前分支上分离开的副本。

11)、头（HEAD）
指向当前选择的分支。

12)、修订（Revision）
表示代码的一个版本状态。Git通过用SHA1 hash算法表示的ID来标识不同的版本。

13)、标记（Tags）
标记某个分支的某一个版本，方便检索和查看。

14)、推送（Push）
向远程仓库推送当前分支

15)、拉取（Pull）
从远程仓库拉取文件，并合并入当前分支。