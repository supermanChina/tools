# GIT命令行常用命令
[参考链接](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

[TOC]

## 基本配置
用户名：  
`git config --global user.name "Your Name"`  
邮箱：  
`git config --global user.email you@example.com`


## clone-将远程仓库clone到本地 
`git clone https://github.com/supermanChina/Python.git`  
将在本地生成一个Python远程仓库目录

## init-在当前目录新创建git本地仓库 
`git init`

## add-添加新文件或目录到本地仓库
  
`git add README.md`
## commit-提交代码到本地仓库
在使用`git add`命令添加变化文件后可使用以下命令提交 
`git commit -m "log message"`  
使用以下命令可以将本地已经修改的文件自动加入到commit列表中而不用通过`git add`添加。  
`git commit -am "log message"`  
*注意：-a参数不能自动添加新增文件。新增文件必须通过git add添加后才能commit*
## remote-列出所有远程主机 
`git remote -v`
```
[james@i200-eng131 Python]$ git remote -v
origin	https://github.com/supermanChina/Python.git (fetch)
origin	https://github.com/supermanChina/Python.git (push)
```
*克隆版本库的时候，所使用的远程主机自动被Git命名为origin*
## remote show-显示远程主机详细信息
`git remote show 主机名`
```
[james@i200-eng131 Python]$ git remote show origin
* remote origin
  Fetch URL: https://github.com/supermanChina/Python.git
  Push  URL: https://github.com/supermanChina/Python.git
  HEAD branch: (unknown)
  Local branch configured for 'git pull':
    master merges with remote master
```

## remote add-添加远程仓库主机  
`git remote add origin https://github.com/supermanChina/Python.git`  
其中URL指向远程GIT仓库路径

## remote rm-删除远程主机  
`git remote rm 主机名`

## push-将本地提交更新推送到远程主机
`git push <远程主机名> <本地分支名>:<远程分支名>`  
以下命令把本地master分支推送到origin远程主机的master分支：  
`git push origin master`  
如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。  
`git push origin`  
如果当前分支只有一个追踪分支，那么主机名都可以省略。  
`git push`

## pull-取回远程主机分支更新并与本地分支合并
`git pull 远程主机名 远程分支名：本地分支名`  
例如以下命令表示从远程主机origin/master分支取回代码与本地master分支合并。  
`git pull origin master:master`  
*`git clone`时会为本地分支master与远程主机分支origin/master建立追踪关系，在存在追踪关系时，可以省略分支名*  
`git pull origin`  
*如果只有一个追踪分支，则远程主机名也可以省略*  
`git pull`

## branch-分支管理
### 查看本地分支：
`git branch`  其中*标识当前工作本地分支  
```
$ git branch  
* br-2.1.2.2  
  master  
```
### 创建本地分支
```
git branch local_branch
```
### 切换到本地分支
```
git checkout local_branch
```
### 将本地分支push到远程分支
只指定本地分支名时，将使用相同的本地分支名建立对应的远程分支名。  
```
C:\github\gitpractice>git push origin local_branch2
fatal: unable to get credential storage lock: File exists
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 563 bytes | 0 bytes/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To https://github.com/supermanChina/gitpractice.git
 * [new branch]      local_branch2 -> local_branch2
```
### 建立本地分支与远程分支的关联tracking
新创建的本地分支和远程分支没有建立关联，使用`git pull`时会报如下信息：
```
C:\github\gitpractice>git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> local_branch2
```
使用如下命令建立本地分支与远程分支的tracking关系：
```
C:\github\gitpractice>git branch --set-upstream-to=origin/local_branch2 local_branch2
Branch local_branch2 set up to track remote branch local_branch2 from origin.
```
### push分支同时建立tracking关联--set-upstream
以下命令在将本地分支bug1推送到远程分支的同时，建立tracking关联： 
```
$ git push --set-upstream origin bug1
fatal: unable to get credential storage lock: File exists
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 875 bytes | 0 bytes/s, done.
Total 6 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/supermanChina/gitpractice.git
   f36f1cf..310f63b  bug1 -> bug1
Branch bug1 set up to track remote branch bug1 from origin.
```
### 查看所有分支：
`git branch -a`  其中*标识当前工作远程分支  
```
$ git branch -a  
* br-2.1.2.2  
  master  
  remotes/origin/HEAD -> origin/master  
  remotes/origin/br-2.1.2.1  
  remotes/origin/br-2.1.2.2  
```
### 查看远程分支
`git branch -r`
### 删除分支-d
删除本地分支
```
$ git branch -d local_branch2
Deleted branch local_branch2 (was 5c6e2e5).
```
删除远程分支
```
$ git branch -d -r origin/local_branch2
Deleted remote-tracking branch origin/local_branch2 (was 5c6e2e5).
```

### 切换到指定远程分支
以下命令从远程origin/dev_branch分支检出到本地dev_branch分支并建立起本地dev_branch与远程origin/dev_branch:  
`git checkout -b dev_branch remotes/origin/dev_branch`