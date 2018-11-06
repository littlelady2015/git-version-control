Git版本控制管理@[TOC]

# git
Git is a distributed version control system.
## 知识点
#### 版本回退
#### 1.工作区和暂存区
工作区： 电脑里的目录
版本库： .git
版本库中含有暂存区(stage)
![工作区/版本库](http://upload-images.jianshu.io/upload_images/9357687-1c6ab7cac54a5061?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
git add 将文件添加到暂存区
git commit 将文件提交到本地分支
###### 划重点

git checkout -- <file>   (注意 这里有一个空格)    用于丢弃工作区的修改
情况1. readme.md 还没有被放到暂存区，撤销修改后和版本库一模一样
情况2. readme.md 已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态

git reset HEAD <file> 将暂存区的修改撤销，放回工作区 
具体而言： git reset调整HEAD引用指向给定的提交，默认情况下还会更新索引以匹配该提交
如果我们希望工作区 暂存区都没有进行修改
先进行 git reset 再进行 git checkout
#### 2.删除文件 git rm 
在文件管理器中删除没用的文件  rm test.txt
此时工作区和版本库就不一致了，git status命令会立刻告诉你哪些文件被删除了：
```
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")

```
`git rm`: 将在版本库和工作目录中同时删除文件
> 注意:   git  rm  是一条对索引进行操作的命令 

`git rm --cached`  可以将误添加的文件恢复为未添加状态
> 删除索引中的文件并把它保留在工作目录中，git rm则会将文件从索引目录和工作目录中都删除
```
$ git status
On branch master
You are currently rebasing branch 'dev' on 'b79e97b'.
  (all conflicts fixed: run "git rebase --continue")

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   readme.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	opps.md
```
#### 3. 移动文件 git mv
如果你需要移动或者重命名文件，可以执行
```
$ mv stuff newstuff
$ git rm stuff
$ git add newstuff
```
天呐，移动一个文件居然三部才能完成？？？？
使用`git mv` 命令 ，将文件stuff 重新命名为 newstuff
`git mv stuff newstuff`
#### 4.查看提交的历史记录 git log
`git log newstuff`
如果你刚好重新命名了一个文件，你会发现除了命名之后的记录之外之前文件的记录都丢了
Git其实是记的全部的历史记录的，但是显示要限制于在命令中指定的文件名； --follow选项会让git在日志中回溯并找到内容相关联的整个历史记录；
`$ git log --follow newstuff`
git log 提交范围
`git log ^ XY ` 等同于 `git log X...Y` 可以认为是集合减法
用Y之前的所有提交减去X之前的所有提交且包括X
范围： (X,Y]
topic...master 表示排除从topic分支可达的提交记录
##### 情境一
![topic并入master](https://upload-images.jianshu.io/upload_images/9357687-b37bd3cb8e292604.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 此时包含的提交记录是 V W X Y Z
##### 情境二
![切分支又合并](https://upload-images.jianshu.io/upload_images/9357687-7b7c2e45ee8c0673.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>  此时包含的提交记录是 W X Y Z
 ### 版本库搜索（强大的命令）
#### 1. 使用git bisect命令 （启动二分搜索）
步骤：
```
//  1. 启动二分搜索，Git进入二分模式，并为自己设置一些状态信息， 一旦启动，需要告诉git哪一个是坏的 
$ git bisect start
// 2. 可以默认使用当前分支提交作为坏提交
$ git bisect bad
// 3. 同样需要告诉git 哪一个是好的
$ git bisect good v1.0.4
```
#### 2. 识别特定提交 git blame
` $ git blame -L 35 , init/version.c`

### 分支管理
#### 1.创建& 合并分支 git checkout / git merge
创建分支实际上是改变指针HEAD的指向，工作区文件没有任何变化
![0.png](https://upload-images.jianshu.io/upload_images/9357687-cec77f4b170f4523.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

合并分支
```
$ git checkout master
Switched to branch 'master'  
$ git merge dev    //将dev合并到master
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```
#### 2.解决冲突
出现在合并分支的时候
我们到底应该保留哪一部分呢？ 到底哪一部分才是最新的修改？ 天啊 我分不清了
此时处于master分支 ，合并了feature1的内容
注意现在
<<<<<< HEAD
=======
这部分的内容 ， 是master分支上的， 下面的这个>>>>>>> feature1 才是合并进来的分支上的内容
```
<<<<<< HEAD
我不是乱码
=======
djadjsjd
>>>>>> feature1
```
#### 图形化查看提交记录
git log --graph 
#### 使用 --no-ff 采取普通模式合并，合并后的历史有分支，能看出曾经做过合并
#### 3 删除分支
1) 删除特定未合并分支 
git branch -d <branch-name>
git可能提示销毁失败，因为分支还没有被合并，如果删除，将丢失更改
2) 强行删除分支
git branch -d <branch-name>
告诉我们一件事情： 开发一个新功能，就新建一个分支
3) 恢复删除的分支
意外删除分支或其它引用后，使用 git reflog 命令来恢复
#### 4. 处理本地和远程分支的关系
例子： 在dev分支上进行开发，必须创建远程 `origin`的dev分支到本地
创建远程dev分支到本地
```
$ git checkout -b dev origin/dev
```
#### 5. rebase 命令
变基操作一次只迁移一个提交，从各自原始提交为止迁移到新的提交基础；
###### 图解
![git rebase之前](https://upload-images.jianshu.io/upload_images/9357687-d380817ce916f976.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![git rebase之后](https://upload-images.jianshu.io/upload_images/9357687-64f9cbeabfff7d38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###### 变基与合并
变基的一系列提交会导致Git生成一系列全新的提交,拥有新的SHA1提交ID，基于新的初始状态，代表不同的差异；
⚠️注意：如果有额外的分支基于你想变基的分支，可能会产生问题；
例子：
![对多分支进行git rebase之前](https://img-blog.csdnimg.cn/20181106100409268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTkxNjYx,size_16,color_FFFFFF,t_70)

```bash
# 将dev分支移动到master分支的头
$ git rebase master dev
```
实际得到的结果
如果你想要的是 将dev_2变基到dev最新的提交Z，意想得到的结果是这样的
![git rebase master dev 之后](https://img-blog.csdnimg.cn/2018110610295558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTkxNjYx,size_16,color_FFFFFF,t_70)
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181106104831944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTkxNjYx,size_16,color_FFFFFF,t_70)
这种情况下，如果你更希望的是移动整个分支（包括该分支的子分支），需要反过来把dev2分支变基到dev分支的新提交Y’上面
```bash
$ git rebase dev^ dev2  
```
###### 重要的几个概念
1. 变基将提交重写成新提交
2. 不可达的旧提交会消失；
3. 任何旧的、变基前的提交的用户可能被困住；
4. 如果有分支用变基前的提交，可能需要反过来对它变基；
5. 如果有用户有不同版本库中变基前的提交，即使它已经移动到你的版本库中，它仍然拥有该提交的副本，该用户现在也必须修复他的提交历史记录。

用书中这个复杂的例子解释一下这几句话：
![复杂的分支](https://img-blog.csdnimg.cn/20181106153516592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTkxNjYx,size_16,color_FFFFFF,t_70)

```bash
$ git rebase master dev
First, rewinding head to replay your work on top of it...
Applying: X
Applying: Y
Applying: Z
Applying: P
Applying: N

 $ git show-branch
 * [dev] N
  ![master] D
  --
  * [dev] N
  * [dev^] P
  * [dev~2] Z
  * [dev~3] Y
  * [dev~4] X
  * [dev~5] D
# 现在所有的提交连成了一串
```
git 应用了所有的（非合并）提交变更；发生的过程：
在master ...dev	范围内找提交。为了列出所有提交，Git对图中的那部分执行拓扑排序，产生该范围内所有提交的一个线性序列；
【其实我也没有明白 - - 】


## diff命令
一个根级别的diiff可以有效的将两个版本库进行同步
显示工作目录和给定提交之间的差异
#### 1. git diff commit  
#### 比较SVN 和 Git 如何产生 diff
 git: 每个修订版本都有自己的一棵树，但git不需要它们来生成diff ， Git可以直接操作两个版本的完整状态快照
svn：跟踪修订一系列版本，只储存文件内的差异 查看r1008 与r1908间的diff，svn会查看两个版本之间所有单独的diff，然后合成一个大的diff，把结果发送给用户
## 合并
#### 1. 合并策略
解决（resolve）： 解决策略只操作两个分支。定位共同的祖先作为合并基础，然后执行一个直接的三方合并；
递归（Recursive）： 递归策略和解决策略相似，每次处理两个分支； 但它可以处理在两个分支之间有多个合并基础的情况； Git生成一个临时合并来包含所有相同的合并基础，以此为基础通过一个普通的三方合并算法导出两个给定分支的最终合并；
章鱼（Octopus）： 专为合并两个以上分支而设计； 在内部它需要多次调用递归合并策略，要合并的每个分支调一次；
#### 2. 应用合并策略
Git会尽可能尝试使用简单廉价的算法，如果可能，首先尝试使用“已经是最新的”和“快进”策略来消除不重要的、简单的情况；
#### 终止或重新启动合并
a. 如果你开始合并，但是因为某种原因你不想完成它，git提供 如下命令来终止合并
` $ git reset --hard HEAD`
这条命令可以立即把工作目录和索引都还原到 git merge 之前的状态
b. 如果要中止或在它已经结束（即 引入一个新的合并提交）后放弃， 使用：
` $ git reset --hard ORIG_HEAD `
在开始合并操作之前，Git将原始分支的HEAD 保存在 ORIG_HEAD ,就是为了这种目的
> 从Git 1.6.1 开始，有另一种选择。
如果你把冲突解决方案搞砸了，并且想再返回到尝试解决前的原始冲突状态，可以使用 git checkout -m
## 更改提交
#### 1. 关于修改历史记录的注意事项
如果一个分支已经公开了，并且可能已经存在于其他版本库中，就不应该重写、修改或者更改该分支的任何部分；
#### 2.git reset命令
`git reset`命令是“破坏性”的，可以覆盖并销毁工作目录中的修改；
#### 3. 使用 git cherry-pick
1) 在当前分支上应用给定提交引入的变更
在正常开发过程中，开发线的提交F修复了一个bug；此时F提交也存在于版本2.3发布版中，可以对rel 2.3分支使用 git cherry-pick 来应用bug修复
![cherry-pick.png](https://upload-images.jianshu.io/upload_images/9357687-3cb661c1262cf1d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
$ git checkout rel_2.3
git cherry-pick dev~2 #commit F , above
```
2) 重建一系列提交
通过从一个分支选一批提交，然后把它们引入一个新分支
以Y ， W ， X ， Z 的顺序应用它们，可以使用如下命令：
![执行cherry-pick 进行乱序之前](https://upload-images.jianshu.io/upload_images/9357687-e3f03f4e340b6f15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
$ git checkout master
$ git cherry-pick my_dev^   #Y
$ git cherry-pick my_dev~3   #W
$ git cherry-pick my_dev~2   #X
$ git cherry-pick my_dev #Z
```
![执行 git cherry-pick之后](https://upload-images.jianshu.io/upload_images/9357687-8833c67e6162a257.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 4. 使用 git revert
与`git cherry-pick`提交命令是大致相同的，但又一个重要区别： 应用于给定提交的逆过程
常见用途是：“撤销”可能深埋在历史记录中的某个提交的影响
` $ git revert master~3` master向前数三个版本撤销
![执行git revert之后](https://upload-images.jianshu.io/upload_images/9357687-4141a95478032909.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 储藏和引用日志
git stash 命令
用于紧急修复bug 但不提交手头的工作
git stash  暂存刚才的改动记录
git stash list 用于查看之前的工作现场
git stash apply 恢复现场
git stash drop 删除之前的修改
git stash pop 在恢复现场之后将stash内的内容删除
git stash apply stash@{0} 多次stash之后恢复指定的stash
## QA
##### 1.为什么不使用 commit -a 或者 commit -m ./ ？
##### 2. 我应该对一系列操作进行合并还是变基




