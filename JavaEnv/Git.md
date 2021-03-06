# Git
##### 1，配置Git开发环境
- 1-1 Windows环境
  - 1-1-1，下载Git客户端压缩包
    - [官网下载地址](https://git-scm.com/)
  - 1-1-2，安装
    - 安装完成，输入`$ git`检查是否安装完成
  - 1-1-3，配置环境变量
    - 新建一个GIT_HOME的环境变量，值为C:\MySoft\Git
    - 修改path值，增加%GIT_HOME%\bin;
- 1-2 Linux环境
  ```
  # 下载后拷贝到服务器并解压
  cd /data/java/git
  tar -zxvf git-2.14.1.tar.gz
  
  # 新增一个git文件夹
  /data/git
  
  # 切换到git解压文件夹中
  cd /data/java/git/git-2.14.1
  
  # 配置git安装路径
  ./configure prefix=/data/git/
  ```
---
##### 2，Git常用命令
- 2-1，相关名词
  - 2-1-1，master：默认开发分支
  - 2-1-2，origin：默认远程版本库
  - 2-1-3，.git文件夹：版本库，其中有个stage的暂存区，git为我们自动创建一个master分支，以及指向master的一个指针叫HEAD
  - 2-1-3，Index/Stage：暂存区
  - 2-1-4，Workspace：工作区，文件夹是一个工作区
  - 2-1-5，Repository：仓库区（或本地仓库）
  - 2-1-6，Remote：远程仓库
- 2-2，新建代码库
```
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [project-url]
```
- 2-3，配置
  - Git的设置文件为.gitconfig，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）
```
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.emal "[email]"
```
- 2-4，增加，删除，修改文件
```
# 查看状态
$ git status

# 查看变更内容
$ git diff

# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录所有文件到暂存区
$ git add .

# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 更改文件名，并且将这个改名放入暂存区
$ git mv [file-old] [file-new]
```
- 2-5，代码提交
```
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commt来替换上一次提交，如果代码没有任何变化，则用来改写上一次commit提交信息
$ git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```
- 2-6，分支
```
# 显示所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch-name] [remote-branch-name]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch-name]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 切换到上一个分支
$ git checkout -

# 在现有分支和指定远程分支之间建立追踪关系
$ git branch --set-upstream [branch-name] [remote-branch-name]

# 合并指定分支到当前分支
$ git merge [branch-name]

# 衍合指定分支到当前分支
$ git rebase [branch-name]

# 选择一个commit合并进当前分支
$ git cherry-pick [commit-id]
```
- 2-7，标签
```
# 列出所有本地标签
$ git tag

# 基于最新提交创建标签
$ git tag [tag-name]

# 删除标签
$ git tag -d [tag-name]

# 删除远程标签
$ git push origin :refs/tags/[tag-name]

# 查看tag信息
$ git show [tag-name]

# 提交指定tag
$ git push [remote] [tag-name]

# 提交所有tag
$ git push [remote] --tags

# 新建一个分支并指向某个tag
$ git checkout -b [branch-name] [tag-name]
```
- 2-8，查看信息
```
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示commit历史以及每次commit发生变更的文件
$ git log --stat

# 只输出commit id和commit info
$ git log --oneline

# 根据关键字搜索提交历史
$ git log -S [keyword]

# 显示某个commit之后所有变动，每个commit占据一行
$ git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后所有变动，提交说明必须符合搜索条件
$ git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]
$ git whatchanged [file]

# 显示指定文件相关的每一次diff
$ git log -p [file]

# 显示过去5次提交
$ git log -5 --pretty --online

# 显示所有提交过的用户，按提交次数排序
$ git shortlog -sn

# 显示指定文件什么人在什么时间修改过
$ git blame [file]

# 显示暂存区和工作区的差异
$ git diff

# 显示暂存区和上一个commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示两次提交之间的差异
$ git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
$ git show [commit]

# 显示某次提交发生变化的文件
$ git show --name-only [commit]

# 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]

# 显示当前分支的最近几次提交
$ git reflog
```
- 2-9，远程操作
```
# 下载远程仓库所有变动
$ git fetch [remote]

# 取回远程仓库的变化并与本地分支合并
$ git pull [remote] [branch]

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库并命名
$ git remote add [name] [url]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all

# 删除远程分支或标签
$ git push <remote> :<branch/tag-name>

# 上传所有标签
$ git push --tags
```
- 2-10，撤销
```
# 回退到当前版本
$ git reset --hard HEAD

# 回退到上一个版本
$ git reset --hard HEAD^

# 回退到上上个版本
$ git reset --hard HEAD^^
$ git reset --hard HEAD~2

# 回退到某个版本
$ git reset --hard <commit id>

# 撤销指定的未提交文件的修改内容
$ git checkout HEAD <file>

# 撤销指定的提交
$ git revert <commit>

# 退回到之前一天的版本
$ git log --before="1 days"

# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

# 新建一个commit，用来撤销指定commit，后者的所有变化都会被前者抵消，并且应用到当前分支
$ git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```
- 2-11，其他
```
$ git archive
```
---

##### 3，常用选项
- `-f` --force：强制
- `-d` --delete：删除
- `-D` --delete --force
- `-m` --move：移动或重命名
- `-M` --move --force
- `-r` --remote：远程
- `-a` --all：所有

##### 4，常用的操作
- 4-1 撤销对工作区中文件的修改
  - 4-1-1 修改，但没有使用git add将修改添加到暂存区
  ```
  $ git checkout --文件
  # 本地所有修改，没有提交的都返回到原来的状态
  $ git checkout .
  # 将所有没提交的修改暂存到stash中，可用git stash pop恢复
  $ git stash
  ```
  - 4-1-2 修改，已经使用git add将修改添加到暂存区
  ```
  # 先撤销缓存区的修改，再撤销工作区修改
  $ git reset HEAD --文件
  $ git checkout --文件
  # 返回到某个节点，不保留修改
  $ git reset --hard HEAD
  # 返回到某个节点，保留修改
  $ git reset --soft HEAD
  ```
  - 4-1-3 修改，已经使用git add将修改添加到暂存区，并再次进行修改
  ```
  # 先撤销工作区修改，再撤销缓存区的修改，再撤销工作区修改
  $ git checkout --文件
  $ git reset HEAD --文件
  $ git checkout --文件
  ```
  - 总结：`$ git checkout --文件`撤销工作区文件修改，`$ git reset HEAD --文件`撤销暂存区中文件的修改
- 4-2 撤销commit但没有push的代码
  ```
  # 找到之前提交的git commit的id
  $ git log
  
  # 完成撤销，同时将代码恢复到此commit id对于的版本
  $ git reset --hard [commit-id]
  
  # 完成撤销，停留在当前版本，不对代码修改进行撤销，可用直接commit或者重新修改代码
  $ git reset [commit-id]
  ```
- 4-3 修改.gitignore文件后，重新追踪文件
  ```
  $ git rm -r --cached .
  $ git add .
  $ git commit -m 'update .gitignore'
  ```
- 4-3 清空工程
  ```
  $ git rm -rf .
  ```
- 4-4 每隔X秒运行一次git pull
  ```
  $ for((i=1;i<=10000;i+=1)); do sleep X && git pull; done
  ```
- 4-5 使用git rebase将一个feature分支变基到master分支
  ```
  $ git checkout feature
  $ git rebase master
  ```
