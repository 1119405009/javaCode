## Docker 简介

###### Docke定义

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows机器上。使用Docker可以更方便低打包、测试以及部署应用程序。

###### 官网

docker hub的官网，地址：[https://hub.docker.com](https://hub.docker.com/)



## Docker 环境安装



###### 安装yum-utils

`yum install -y yum-utils device-mapper-persistent-data lvm2`

######   为yum源添加docker仓库位置

​       `yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`

###### 安装docker

​       `yum install docker-ce`

###### 启动docker

​      `systemctl start docker`



## Docker 镜像常用命令

###### 搜索镜像

`docker search java`

###### 下载镜像

`docker pull java:8`

###### 列出镜像

`docker images`

###### 删除镜像

`docker rmi java:8`

###### 指定名称删除镜像（强制）

`docker rmi -f java:8`

###### 删除所有没有引用的镜像

`docker rmi docker images | grep none | awk '{print $3}'`

###### 强制删除所有镜像

`docker rmi -f $(docker images)`

## Docker 容器常用命令



###### 新建并启动容器

`docker run -p 80:80 --name nginx -d nginx:1.17.0`

`-d选项：表示后台运行`
`--name选项：指定运行后容器的名字为nginx,之后可以通过名字来操作容器`
`-p选项：指定端口映射，格式为：hostPort:containerPort`

###### 列出容器

-   列出运行中的容器

   `docker ps`

-  列出所有容器

   `docker ps -a`



###### 停止容器

`#$ContainerName及$ContainerId可以用docker ps命令查询出来`

`docker stop $ContainerName(或者$ContainerId)`

`例如 ：docker stop nginx  #或者  docker stop c5f5d5125587`

- 强制停止容器
  `docker kill $ContainerName(或者$ContainerId)`



- 启动已停止的容器

`docker start $ContainerName(或者$ContainerId)`



###### 进入容器

-    先查询出容器的pid
     `docker inspect --format "{{.State.Pid}}" $ContainerName(或者$ContainerId)`

-    根据容器的pid进入容器

   `nsenter --target "$pid" --mount --uts --ipc --net --pid`

- 删除容器

-   删除指定容器

  `docker rm $ContainerName(或者$ContainerId)`

-   按名称删除容器

  `docker rm docker ps -a | grep mall-* | awk '{print $1}'`

-   强制删除所有容器

  `docker rm -f $(docker ps -a -q)`



###### 查看容器的日志

-    查看当前全部日志

   `docker logs $ContainerName(或者$ContainerId)`

-    动态查看日志

  `docker logs $ContainerName(或者$ContainerId) -f`

- 查看容器的IP地址

`docker inspect --format '{{ .NetworkSettings.IPAddress }}' $ContainerName(或者$ContainerId)`

- 修改容器的启动方式

`docker container update --restart=always $ContainerName`

- 同步宿主机时间到容器

`docker cp /etc/localtime $ContainerName(或者$ContainerId):/etc/`



###### 在宿主机查看docker使用cpu、内存、网络、io情况

- ​    查看指定容器情况

​    `docker stats $ContainerName(或者$ContainerId)`

- ​	查看指定容器情况 

​	`docker stats $ContainerName(或者$ContainerId)`

- ​	查看所有容器情况

​	`docker stats -a`

- ​	查看Docker磁盘使用情况

​	`docker system df`

- ​	进入Docker容器内部的bash

​	`docker exec -it $ContainerName /bin/bash`

- ​	Docker创建外部网络

​	`docker network create -d bridge my-bridge-network`



###### 修改Docker镜像的存放位置

- ​	查看Docker镜像的存放位置

​	`docker info | grep "Docker Root Dir"`

- ​	关闭Docker服务

​	`systemctl stop docker`

- ​	移动目录到目标路径

​	`mv /var/lib/docker /mydata/docker`

- ​	建立软连接

​	`ln -s /mydata/docker /var/lib/docker`

















