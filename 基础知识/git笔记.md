# git笔记

[toc]

### 相关概念

- Workspace：工作区
- Index/Stage：暂存区
- Repository：仓库区
- Remote：远程仓库

### 常用操作

#### 新建代码库

##### 在当前目录新建一个仓库

`git init`

##### 新建一个目录，并初始化为git仓库

`git init  <project-name>`

##### 克隆一个仓库到当前目录下

`git clone <url>` 

#### 代码提交

##### 添加指定文件到暂存区

`git add <file1> [file2 ...]`

##### 添加指定目录到暂存区，包括子目录

`git add <dir>`

##### 添加当前目录所有文件到暂存区

`git add .`

##### 提交暂存区代码到仓库区

`git commit -m <message>`

##### 提交暂存区指定代码到仓库区

`git commit <file1> [file2 ...] -m <message>`

##### 提交工作区自上次commit之后到变化到仓库区

`git commit -a`

#### 分支

##### 列出所有本地分支

`git branch`

##### 列出所有远程分支

`git branch -r`

##### 列出所有本地分支和远程分支

`git branch -a`

##### 新建一个分支，但依然停留在当前分支

`git branch <branch>`

##### 新建一个分支，并切换到该分支

`git checkout -b <branch>`

##### 新建一个分支，指向指定commit

`git branch <branch> <commit>`

##### 切换到指定分支，并更新工作区

`git checkout <branch>`

##### 合并指定分支到当前分支

`git merge <branch>`

##### 选择一个commit，合并进当前分支

`git cherry-pick <commit>`

##### 删除分支

`git branch -d <branch>`

##### 删除远程分支

`git push origin -delete <branch>`

`git branch -dr <remote/branch>`

#### 标签

##### 列出所有tag

`git tag`

##### 新建一个tag在当前commit

`git tag <tag>`

##### 新建一个tag在指定commit

`git tag <tag> <commit>`

##### 删除本地tag

`git tag -d <tag>`

##### 查看tag信息

`git show <tag>`

#### 查看信息

##### 显示有变更的文件

`git status`

##### 显示当前分支的版本历史

`git log`

##### 显示暂存区和工作区的差异

`git diff`

##### 显示工作区与当前分支最新commit之间的差异

`git diff HEAD`

##### 显示当前分支的最近几次提交

`git reflog`

#### 撤销

##### 恢复暂存区的指定文件到工作区

`git checkout <file>`

##### 恢复某个commit到指定文件到暂存区和工作区

`git checkout <commit> <file>`

##### 回滚到指定commit

`git reset --soft <commit>` 回滚归档区

`git reset --mixed <commit>` 回滚归档去和暂存区

`git reset --hard <commit>` 回滚归档去、暂存区、工作区

##### 新建一个commit，用来撤销指定commit，即将后者的改动全部恢复

`git revert <commit>`

#### 远程同步

##### 增加一个新的远程仓库并命名

`git remote add <remote_name> <url>`

##### 下载远程仓库到所有变动

`git fetch <remote>`

##### 取回远程仓库到变化，并与本地分支合并

`git pull <remote> <branch>`

##### 推送本地指定分支到远程仓库

`git push <remote> <branch>`

### 参考资料

[常用 Git 命令清单](https://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)