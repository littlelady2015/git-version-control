## git
Git is a distributed version control system.
## 知识点
### 版本回退
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
#### 3.git stash 命令
用于紧急修复bug 但不提交手头的工作
git stash  暂存刚才的改动记录
git stash list 用于查看之前的工作现场
git stash apply 恢复现场
git stash drop 删除之前的修改
git stash pop 在恢复现场之后将stash内的内容删除
git stash apply stash@{0} 多次stash之后恢复指定的stash

#### 4. 删除分支
1) 删除特定未合并分支 
git branch -d <branch-name>
git可能提示销毁失败，因为分支还没有被合并，如果删除，将丢失更改
2) 强行删除分支
git branch -d <branch-name>
告诉我们一件事情： 开发一个新功能，就新建一个分支
3) 恢复删除的分支
意外删除分支或其它引用后，使用 git reflog 命令来恢复
#### 5. 处理本地和远程分支的关系
例子： 在dev分支上进行开发，必须创建远程 `origin`的dev分支到本地
创建远程dev分支到本地
```
$ git checkout -b dev origin/dev
```
#### 6. rebase 命令
使git的提交记录为一条直线
rebase 变基操作

## QA
##### 1.为什么不使用 commit -a 或者 commit -m ./ 








