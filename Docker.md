# docker 笔记

## 查看本机安装的image

```bash
$ docker images 
```

## 运行container

```bash
$ docker run -p 8080:80 -d daocloud.io/nginx
```

run 运行
-p  吧本机8080映射到容器80
-d  运行那个一个image

## 查看在运行的

```bash
$ docker ps
$ docker ps -a  // 列出之前运行过的
```

## 本地拷贝到容器文件

```bash
$ docker cp index.html 容器ID://容器内的路径
```

## 停止容器

```bash
$ docker stop 容器ID
```

> 在容器内更改都是暂时的 都是没有被保存的

## 保存更改过的容器内容

```bash
docker commit -m 'fun' 容器的ID
docker commit -m 'fun' 容器的ID  容器的名字
```

## 命令小结

|     命令      |             用途              |
| :-----------: | :---------------------------: |
|  docker pull  |           获取image           |
| docker build  |           创建image           |
| docker images |           列出image           |
|  docker run   |         运行container         |
|  docker stop  |         停止container         |
|   docker ps   |         列出container         |
|   docker rm   |         删除container         |
|  docker rmi   |           删除image           |
|   docker cp   | 在host和container之间拷贝文件 |
| docker commit |      保存改动为新的image      |



------



## Liunx安装docker

### 使用命令

```bash
$ sudo wget -qO- https://get.docker.com/ | sh
```

## 第一个Dockerfile

### 设置继承自我们创建的 tools 镜像

```
FROM alpine:latest
```

### 下面是一些创建者的基本信息

```
MAINTAINER xbf (邮箱地址)
```

### 执行

```
CMD echo 'hello docker'
```

```bash
$ docker build -t hell_docker .
```

-t 给他一个标签  标签名字：hell_docker
. 是路径  运行这个路径下



## 第二个Dockerfile

```config
FROM ubuntu
MAINTAINER uyoung
RUN apt-get update
RUN apt-get install -y nginx
COPY index.html /var/www/html  // 拷贝一个本地的文件到里面去
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"] // 给出一个入口(不明白)  将nginx在前台执行而不是守护进程

EXPOSE 80
```

```bash
$ docker run -d -p 80:80 uyoung/hello_nginx
```

## Dockerfile 语法小结

|    命令    |     用途     |
| :--------: | :----------: |
|    FROM    |  base image  |
|    RUN     |   执行命令   |
|    ADD     |   添加文件   |
|    COPY    |   拷贝文件   |
|    CMD     |   执行命令   |
|   EXPOSE   |   暴漏端口   |
|  WORKDIR   |   指定路径   |
| MAINTAINER |    维护者    |
|    ENV     | 设定环境变量 |
| ENTRYPOINT |   容器入口   |
|    USER    |   指定用户   |
|   VOLUME   | mount point  |



------





## Volume

### 第一种

```bash
$ docker run -v /usr/share/nginx/html nginx
```

这种方式 可以吧数据库文件挂载到容器外

### 执行的这个

```bash
$ docker run -d --name nginx -v /usr/share/nginx/html nginx
$ docker inspect nginx
```

吧宿主机(Source) /var/lib/docker/volumes/[base64]/_data
挂载到容器内部的(Destination)  /usr/share/nginx/html

$ screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-liunx/tty

### 快速修改文件

```bash
$ echo "内容" > index.html
```

### 进入容器查看

``` bash
$ docker exec -it nginx /bin/bash
```

exec 
-it
nginx 容器名

### 第二种

创建一个仅有数据的容器 挂载到其他容器
$ docker run -volumes-from ...

#### 实际使用的

```bash
$ docker create -v $PWD/data:/var/mydata --name data_container ubuntu

$ docker run -it --volumes-from data_container ubuntu /bin/bash
```

### 术语

|  English  |   中文   |
| :-------: | :------: |
|   host    |  宿主机  |
|   image   |   镜像   |
| container |   容器   |
| registry  |   仓库   |
|  daemon   | 守护程序 |
|  chlient  |  客户端  |



## 补充

**停止所有的container，这样才能够删除其中的images：**

```bash
$ docker stop $(docker ps -a -q)
```

**如果想要删除所有container的话再加一个指令：**

```bash
$ docker rm $(docker ps -a -q)
```

**MongoDB的设置**

```bash
$ docker run -it --name node -v /my/own/datadir:/data --link mongo:mongo -w /data -p 5000:5000 node bash
```

**进入容器的bash模式**

```bash
$ docker exec -it 容器名称 bash 
```



