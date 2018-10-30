## git
Git is a distributed version control system.
## 知识点
###版本回退
#### 1.工作区和暂存区
工作区： 电脑里的目录
版本库： .git
版本库中含有暂存区(stage)
![工作区/版本库](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)
git add 将文件添加到暂存区
git commit 将文件提交到本地分支
###### 划重点

git checkout -- <file>   (注意 这里有一个空格)    用于丢弃工作区的修改
情况1. readme.md 还没有被放到暂存区，撤销修改后和版本库一模一样
情况2. readme.md 已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态

git reset HEAD <file> 将暂存区的修改撤销，放回工作区
如果我们希望工作区 暂存区都没有进行修改
先进行 git reset 再进行 git checkout
#### 2.删除文件
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
### 分支管理
#### 1.创建& 合并分支
创建分支实际上是改变指针HEAD的指向，工作区文件没有任何变化
![切换到分支dev并进行开发](https://cdn.liaoxuefeng.com/cdn/files/attachments/0013849088235627813efe7649b4f008900e5365bb72323000/0)
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
〈〈〈〈〈〈HEAD
=======
这部分的内容 ， 是master分支上的， 下面的这个 〉〉〉〉〉 feature1 才是合并进来的分支上的内容
```
〈〈〈〈HEAD
我不是乱码
=======
djadjsjd
〉〉〉〉〉 feature1
```
#### 图形化查看提交记录
git log --graph 
#### 使用 --no-ff 采取普通模式合并，合并后的历史有分支，能看出曾经做过合并















