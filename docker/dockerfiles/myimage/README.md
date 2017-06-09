# Dockerfile
[TOC]
## 构建简单镜像
- 创建目录myimage存放Dockerfile
- 编写Dockerfile
FROM 指定父级镜像  
MAINTAINER 描述维护者信息(可选)  
CMD 用来设置启动容器时的默认运行命令。  
```
[root@rhel7 myimage]# cat Dockerfile 
FROM centos:latest
MAINTAINER James Zhang <james@163.com>
CMD echo "Build my image from docker file."
```
- 运行build构建镜像
其中`-t`参数指定镜像仓库名和tag
```
[root@rhel7 myimage]# docker build -t myimage:v1 .
Sending build context to Docker daemon  2.048kB
Step 1/3 : FROM centos:latest
 ---> 8140d0c64310
Step 2/3 : MAINTAINER James Zhang <james@163.com>
 ---> Running in d4a4b7d3012e
 ---> af7c53a25bcf
Removing intermediate container d4a4b7d3012e
Step 3/3 : CMD echo "Build my image from docker file."
 ---> Running in 3d2a3d89cb51
 ---> 0f1594acbcef
Removing intermediate container 3d2a3d89cb51
Successfully built 0f1594acbcef
Successfully tagged myimage:v1
```
- 查看images中已生成新镜像
```
[root@rhel7 myimage]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
myimage             v1                  0f1594acbcef        9 seconds ago       193MB
```
- 运行新镜像
将在容器启动后执行CMD中的命令。
```
[root@rhel7 myimage]# docker run myimage:v1
Build my image from docker file.
````
## 
