# docker
[TOC]

## 容器
容器就是拥有受限资源和单独命名空间的进程。docker是基于linux的CGroup和Namespace技术来实现容器，并通过LayeredFS实现容器的文件系统。
## 安装
### 基于centos/redhat

### 基于windows

## 基本操作
### 镜像管理
#### images-查看本地镜像
docker images
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              7b9b13f7b9c0        2 days ago          118MB
hello-world         latest              48b5124b2768        4 months ago        1.84kB
```
### pull-下载镜像
`docker pull imageName`  
从docker hub下载指定镜像到本地： 
`docker pull ubuntu`将下载ubuntu镜像到本地。下载完成后通过`docker images`可以查看到。

### run-运行docker镜像

#### 运行后立刻结束
docker run
```
$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```
#### -it 运行后进入shell交互
通过`-it`选项运行交互式shell
```
$  docker run -it ubuntu /bin/bash
root@3019ef2db213:/#
```
#### --name指定容器名称
```
[root@rhel7 myimage]# docker run -it --name mycentos centos /bin/bash
[root@rhel7 myimage]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
bd693e3805cb        centos              "/bin/bash"         55 seconds ago      Up 54 seconds                           mycentos
```
#### -d 后台运行容器
通过`-d`参数让窗口运行在后台  
```
[james@i200-eng131 ~]$ docker run -d ubuntu /bin/sh -c "while true; do echo Hello world; sleep 2; done;"
74d5c9e6f15874dc3471b6590d1738f8257ef1a65e8b8b121f06381e9e12be3e
```
#### logs-查看容器日志
通过`docker logs`查看后台容器标准输出，注意其中的参数为docker run启动时输出的container id。  
```
[james@i200-eng131 ~]$ docker logs 74d5
Hello world
Hello world
```
`docker logs -f containerNameOrId`查看实时输出，类似`tail -f file`命令输出。
#### --rm 自动删除
默认情况下容器退出后，并不会被删除，如果通过docker ps -a，可以看到
所有的容器，包括运行中的和停止的。通过--rm 可以在容器退出时自动删除容器，
方便清楚残留的文件。  
#### -v 指定文件卷映射
`－v hostPath:containerPath`指定将主机路径映射到容器的指定路径，从而实现在容器内直接访问主机文件系统。
```
[root@rhel7 ~]# docker run -it -v /root:/hostroot centos
```
通过exec进入容器后查看/hostroot内容会发现与主机的/root一样。在容器对/hostroot的修改会直接修改主机/root中对应的文件。

### exec-进入后台运行容器交互shell
`docker exec -it 容器ID /bin/bash`进入后台容器的交互shell命令行。

### 暂时退出容器返回host
在运行docker容器命令行后，通过`CTRL+pq`退出当前容器环境返回宿主机。
### ps-查看当前运行的镜像
`docker ps`查看当前正在运行的容器。  
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
3019ef2db213        ubuntu              "bash"              9 minutes ago       Up 9 minutes                            quirky_austin
```
`docker ps -a` 查看所有的容器，包括已经退出的容器。  

### attach-重新进入已运行容器
docker attach 已运行容器ID
```
$ docker attach 3019ef2db213
root@3019ef2db213:/#
```
### start-启动容器
`docker start 容器ID`
### restart-重启容器
`docker restart 容器ID`
### stop-停止容器
`docker stop 容器ID`
### kill-强行停止容器
`docker kill 容器ID`

### rm-删除容器
以下命令删除所有退出状态容器。
```
docker rm $(docker ps -a -q)
```

## 五种网络模式
- NONE
- Container
- Host
- Bridge (default mode)
- Overlay

### 使用默认bridge模式进行服务导出
#### -p指定容器内部端口
- 指定容器内部端口号为4444  
```
[james@i200-eng131 ~]$ JOB=$(docker run -d -p 4444 mycentos /usr/bin/nc -l 4444)
[james@i200-eng131 ~]$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
acc4f4422558        mycentos            "/usr/bin/nc -l 4444   6 seconds ago       Up 4 seconds        0.0.0.0:32773->4444/tcp   berserk_hypatia  
```
- 查看容器导出端口
容器内部4444端口映射为主机端口32773
```
[james@i200-eng131 ~]$ docker port $JOB
4444/tcp -> 0.0.0.0:32773
```

- 通过主机发送hello到容器
在主机执行以下命令通过主机映射端口32773向容器的4444发送消息。  
```
[james@i200-eng131 ~]$ echo hello | nc 127.0.0.1 32773
```

- 确认容器收到消息
```
[james@i200-eng131 ~]$ docker logs acc
hello
```

## commit-提交容器到镜像
在容器中做了任何修改，例如安装了新的软件包，在文件系统中修改了文件等，如果退出容器运行则所有的修改都丢失了。重新运行镜像又变成了修改前的状态。  
为了将修改持久化，需要提交容器到镜像文件中。  
- 首先通过`docker ps`查询到容器ID
- 然后通过`docker commit`提交指定容器id到镜像
- 通过`docker images`可以查看到新生成的镜像
```
[james@i200-eng131 shell]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
c3afa621be32        centos              "/bin/bash"         About a minute ago   Up About a minute                       boring_cray         
[james@i200-eng131 shell]$ docker commit c3afa621be32 mycentos
2b5b4b59450c7410834b14dbc3fea6d2c4af0b8e16ccb0cc389fbb7a091760b2
[james@i200-eng131 shell]$ docker images
REPOSITORY                                            TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
mycentos                                              latest              2b5b4b59450c        34 seconds ago      286.3 MB
```
commit时可以同时指定要生成的镜像tag:
```
[root@rhel7 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
c3f86497696b        ubuntu              "/bin/bash"         6 minutes ago       Up 6 minutes                            jolly_gates
b393dc64f277        centos              "/bin/bash"         43 hours ago        Up 43 hours                             suspicious_morse
[root@rhel7 ~]# docker commit -m "sqlite install" --author "james" b39 mycentos:v1
sha256:7802da0ed88d985cba552a9865fb3d73b0c133142b0c33d91f89b6d0af7db695
[root@rhel7 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mycentos            v1                  7802da0ed88d        7 seconds ago       289MB
```
## rmi-删除镜像
`docker rmi containerIdOrName`删除指定ID镜像。  
`docker rmi －f containerIdOrName`强制删除指定ID镜像。

## export-导出容器快照到本地
`docker export containterNameOrId`将容器文件系统以tar包格式导出到标准输出。  
通过重定向`> filename.tar`或者`-o filename.tar`输出到指定的tar文件。  
```
[root@rhel7 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b393dc64f277        centos              "/bin/bash"         25 hours ago        Up 25 hours                             suspicious_morse
[root@rhel7 ~]# docker export b39 > centos1.tar
[root@rhel7 ~]# ll -h centos1.tar 
-rw-r--r--. 1 root root 265M Jun  8 14:37 centos1.tar
```
## import-导入容器快照tar包文件为镜像
将本地文件centos1.tar导入为localcentos:v1镜像。  
```
[root@rhel7 ~]# docker import - localcentos:v1 < centos1.tar
sha256:5ee8dd4c0b095cc84154bb3f44736844a45419cf3482efb519edd10679621244
[root@rhel7 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
localcentos         v1                  5ee8dd4c0b09        18 seconds ago      269MB
```
## Dockerfile构建镜像
### 构建简单镜像示例
参考[简单镜像示例](dockerfiles/myimage/README.md)