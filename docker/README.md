# docker
[TOC]

## 容器
容器就是拥有受限资源和单独命名空间的进程。docker是基于linux的CGroup和Namespace技术来实现容器，并通过LayeredFS实现容器的文件系统。
## 安装
### 基于centos/redhat


## 基本操作
### images-查看本地镜像
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
#### -d 后台运行容器
通过`-d`参数让窗口运行在后台  
```
[james@i200-eng131 ~]$ docker run -d ubuntu /bin/sh -c "while true; do echo Hello world; sleep 2; done;"
74d5c9e6f15874dc3471b6590d1738f8257ef1a65e8b8b121f06381e9e12be3e
```
通过`docker logs`查看后台容器标准输出，注意其中的参数为docker run启动时输出的container id。  
```
[james@i200-eng131 ~]$ docker logs 74d5
Hello world
Hello world
```
### 暂时退出容器返回host
在进行docker容器命令行后，通过`CTRL+pq`退出当前容器环境返回宿主机。
### ps-查看当前运行的镜像
docker ps
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
3019ef2db213        ubuntu              "bash"              9 minutes ago       Up 9 minutes                            quirky_austin
```
### attach-重新进入已运行容器
docker attach 已运行容器ID
```
$ docker attach 3019ef2db213
root@3019ef2db213:/#
```
### restart-重启容器
`docker restart 容器ID`
### stop-停止容器
`docker stop 容器ID`
### kill-强行停止容器
`docker kill 容器ID`
## 五种网络模式
- NONE
- Container
- Host
- Bridge (default mode)
- Overlay
## commit-提交容器到镜像
在容器中做了任何修改，例如安装了新的软件包，在文件系统中修改了文件等，如果退出容器运行则所有的修改都丢失了。重新运行镜像又变成了修改前的状态。  
为了将修改持久化，需要提交容器到镜像文件中。  
- 首先通过`docker ps`查询到容器ID
- 然后通过`docker commit`提交容器到镜像
- 通过`docker image`可以查看到新生成的镜像
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