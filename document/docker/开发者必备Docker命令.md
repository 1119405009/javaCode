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



## 使用Docker Compose部署SpringBoot应用

##### 下载Docker Compose

```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```



#### 修改该文件的权限为可执行

```shell
chmod +x /usr/local/bin/docker-compose
```

#### 查看是否已经安装成功

```shell
docker-compose --version
```



#### 使用Docker Compose的步骤

- 使用Dockerfile定义应用程序环境，一般需要修改初始镜像行为时才需要使用；
- 使用docker-compose.yml定义需要部署的应用程序服务，以便执行脚本一次性部署；
- 使用docker-compose up命令将所有应用服务一次性部署起来。









### docker-compose.yml常用命令

#### image

指定运行的镜像名称

```yml
# 运行的是mysql5.7的镜像
image: mysql:5.7
```

#### container_name

配置容器名称

```yml
# 容器名称为mysql
# 运行的是mysql5.7的镜像
container_name: mysql:5.7
```

### ports

指定宿主机和容器的端口映射（HOST:CONTAINER）

```yml
# 将宿主机的3306端口映射到容器的3306端口
ports:
  - 3306:3306
```

### volumes

将宿主机的文件或目录挂载到容器中（HOST:CONTAINER）

```yml
# 将外部文件挂载到myql容器中
volumes:
  - /mydata/mysql/log:/var/log/mysql
  - /mydata/mysql/data:/var/lib/mysql
  - /mydata/mysql/conf:/etc/mysqlCopy to clipboardErrorCopied
```

### environment

配置环境变量

```yml
# 设置mysqlroot帐号密码的环境变量
environment:
  - MYSQL_ROOT_PASSWORD=root
```

### links

连接其他容器的服务（SERVICE:ALIAS）

```yml
# 可以以database为域名访问服务名称为db的容器
links:
  - db:database
```

#### Docker Compose常用命令

### 构建、创建、启动相关容器

```shell
# -d表示在后台运行
docker-compose up -d
```

### 停止所有相关容器

```shell
docker-compose stop
```

#### 列出所有容器信息

```shell
docker-compose ps
```



#### 使用Docker Compose 部署应用

```
version: "2.0"
services:
  detection-driver:
    image: docker-registry.hzqykeji.com:5000/qiyunkeji/driver
    restart: always
    environment:
      JAVA_OPTS: "-Xmx2048m"
      DOMAIN: "https://inspection-driver-api-qa.hzqykeji.com/"
      REDIS_HOST: "10.8.0.8"
      REDIS_PORT: "6379"
      REDIS_PASSWORD: ""
      REDIS_DATABASE: "15"
      MYSQL_HOST_AND_PORT: "10.8.0.8"
      MYSQL_USERNAME: "root"
      MYSQL_PASSWORD: "root"
      RABBITMQ_HOST: "10.8.0.8"
      RABBITMQ_PORT: "5672"
      RABBITMQ_USERNAME: "guest"
      RABBITMQ_PASSWORD: "guest"
      RABBITMQ_VHOST: "/"
      DEBUG: "true"
      TRACE: "false"
      LOGGING_LEVEL_SQL: "ERROR"
      LOGGING_LEVEL_TYPE: "ERROR"
      INVOICE_URL: "https://dev.fapiao.com:18944/fpt-dsqz/invoice"
      BANK_PAY_NOTIFY: "https://inspection-driver-api-qa.hzqykeji.com/pay/notify"
    volumes:
      - /data/develop/detection-driver/logs:/root/logs
    ports:
    - "592:80"

```



### 以下是部署到测试环境的命令

****

##### 这样打包可以将测试环境镜像放到真实环境

###### 打包并上传

```
mvn install -Dmaven.test.skip=true
```



###### 镜像打包成tar

```
docker save -o detection-admin.tar docker-registry.hzqykeji.com:5000/qiyunkeji/admin
```



###### 解压tar 并指定

```
docker load -i detection-admin.tar && docker-compose -f /data/develop/detection-admin/docker-compose.yml up -d && docker system prune
```



###### docker-compose修改参数重新运行

```
docker-compose -f /data/develop/detection-app/docker-compose.yml up -d
```



## 使用Maven插件构建Docker镜像



### [使用Maven插件构建Docker镜像](https://mp.weixin.qq.com/s/q2KDzHbPkf3Q0EY8qYjYgw)









