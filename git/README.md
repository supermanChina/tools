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
  
`git commit -m "log message"`

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
### 查看远程分支：
`git branch -a`  其中*标识当前工作远程分支  
```
$ git branch -a  
* br-2.1.2.2  
  master  
  remotes/origin/HEAD -> origin/master  
  remotes/origin/br-2.1.2.1  
  remotes/origin/br-2.1.2.2  
```
### 切换到指定远程分支
以下命令从远程origin/dev_branch分支检出到本地dev_branch分支并建立起本地dev_branch与远程origin/dev_branch:  
`git checkout -b dev_branch remotes/origin/dev_branch`