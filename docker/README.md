# Docker

```shell
docker images #查看所有本地主机上的镜像 可以使用docker image ls代替

docker search #搜索镜像

docker pull #下载镜像 docker image pull

docker rmi -f [imageId] #删除指定id的镜像

docker rmi -f $(docker images -aq) #删除全部的镜像
```

## 新建容器并启动

```shell
docker run [options] image | docker container run [可选参数] image

docker run -it centos /bin/bash

#参书说明
--name="Name"  #容器名字 tomcat01 tomcat02 用来区分容器
-v      # 映射、容器卷 , -v /data:/data1  主机的目录 /data 映射到容器的 /data1。
-d      #后台方式运行
-it     #使用交互方式运行，进入容器查看内容
-p      #指定容器的端口 -p 8080(宿主机):80(容器), 支持多个
-P(大写)     # 随机指定端口? 打开容器公开的每个端口?

-e username="prod": #设置环境变量；

--env-file=[]: #从指定文件读入环境变量；

--privileged=true # 给容器扩展权限 centos7 需要配合  /sbin/init
# Failed to get D-Bus connection: Operation not permitted

# Docker 的设计理念是在容器里面不运行后台服务，容器本身就是宿主机上的一个独立的主进程，也可以间接的理解为就是容器里运行服务的应用进程。一个容器的生命周期是围绕这个主进程存在的，所以正确的使用容器方法是将里面的服务运行在前台。

# 再说到 systemd，这个套件已经成为主流 Linux 发行版（比如 CentOS7、Ubuntu14+）默认的服务管理，取代了传统的 SystemV 风格服务管理。systemd 维护系统服务程序，它需要特权去会访问 Linux 内核。而容器并不是一个完整的操作系统，只有一个文件系统，而且默认启动只是普通用户这样的权限访问 Linux 内核，也就是没有特权，所以自然就用不了！

# 因此，请遵守容器设计原则，一个容器里运行一个前台服务！

```

## 列出所有运行的容器

```shell
docker ps 命令    #列出当前正在运行的容器
  -a, --all       #列出当前正在运行的容器 + 带出历史运行过的容器
  -n=?, --last int   #列出最近创建的?个容器 ?为1则只列出最近创建的一个容器,为2则列出2个
  -q, --quiet        #只列出容器的编号
```

## 启动和停止容器的操作

```shell
docker start [containerId] #启动容器
docker restart [containerId] #重启容器
docker stop [containerId] #停止当前正在运行的容器
docker kill [containerId] #强制停止当前容器
1234
```

## 删除容器

```shell
docker rm [containerId]       #删除指定的容器，不能删除正在运行的容器，如果要强制删除 rm -rf
docker rm -f $(docker ps -aq)    #删除所有的容器
docker ps -a -q|xargs docker rm  #删除所有的容器
123
```

## 查看镜像的元数据日志

```shell
# 命令
docker inspect [containerId]

docker logs -t --tail n [containerId] #查看n行日志
docker logs -ft [containerId] #跟着日志
```

## 进入容器

```shell
docker exec -it [containerId] /bin/bash

# docker 复制
docker cp [containerId]:[容器内路径]  [主机目的路径]
```

## commit 镜像

```shell
docker commit 提交容器成为一个新的副本

# 命令和git原理类似
docker commit -m="desctions" -a="author" [containerId] [REPOSITORY[:TAG]]

```

## DockerFile 的指令,

```shell
FROM    # from:基础镜像，一切从这里开始构建
MAINTAINER   # maintainer:镜像是谁写的， 姓名+邮箱
RUN     # run:镜像构建的时候需要运行的命令
ADD     # add:步骤，tomcat镜像，这个tomcat压缩包！添加内容 添加同目录
WORKDIR    # workdir:镜像的工作目录
VOLUME    # volume:挂载的目录
EXPOSE    # expose:保留端口配置
CMD     # cmd:指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT   # entrypoint:指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD    # onbuild:当构建一个被继承DockerFile这个时候就会运行onbuild的指令，触发指令
COPY    # copy:类似ADD，将我们文件拷贝到镜像中
ENV     # env:构建的时候设置环境变量！

构建
#因为dockerfile命名使用默认命名 因此不用使用-f 指定文件
# docker build -t 名字:版本号 .
docker image build -t [imageName:1.0.1] [path]

#-d:后台运行 -p:暴露端口 --name:别名 -v:绑定路径
docker run -d -p 8080:8080 --name tomcat01
```

## 发布镜像

```shell
docker login -u {username} -p [password]

# 会发现push不上去，因为如果没有前缀的话默认是push到 官方的library
# 解决方法：
# 第一种 build的时候添加你的dockerhub用户名，然后在push就可以放到自己的仓库了
$ docker build -t [username]/[REPOSITORY[:TAG]].

# 第二种 使用docker tag #然后再次push
$ docker tag [containerId] [username]/[REPOSITORY[:TAG]] #然后再次push
$ docker push [username]/[REPOSITORY[:TAG]]


#发布到阿里云
$ sudo docker login --username=zchengx registry.cn-shenzhen.aliyuncs.com
$ sudo docker tag [ImageId] registry.cn-shenzhen.aliyuncs.com/dsadxzc/cheng:[镜像版本号]

# 修改id 和 版本
sudo docker tag a5ef1f32aaae registry.cn-shenzhen.aliyuncs.com/dsadxzc/cheng:1.0
# 修改版本
$ sudo docker push registry.cn-shenzhen.aliyuncs.com/dsadxzc/cheng:[镜像版本号]
```

## docker 镜像导出导入区别

```
# https://zhuanlan.zhihu.com/p/152219012
# docker：export/save/commit
```

## 网络

```shell
docker network create --driver overlay --attachable --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
```

## Docker Swarm

```shell
docker swarm init --advertise-addr 主机ip
```

## Docker Stack

```shell
docker stack deploy -c flink.yml flink

docker stack rm flink
```

## Docker 容器日志

```shell
$ docker logs [OPTIONS] CONTAINER
  Options:
        --details        显示更多的信息
    -f, --follow         跟踪实时日志
        --since string   显示自某个timestamp之后的日志，或相对时间，如42m（即42分钟）
        --tail string    从日志末尾显示多少行日志， 默认是all
    -t, --timestamps     显示时间戳
        --until string   显示自某个timestamp之前的日志，或相对时间，如42m（即42分钟）
```

例子：

查看指定时间后的日志，只显示最后 100 行：

```shell
$ docker logs -f -t --since="2018-02-08" --tail=100 CONTAINER_ID
```

查看最近 30 分钟的日志:

```shell
$ docker logs --since 30m CONTAINER_ID
```

查看某时间之后的日志：

```shell
$ docker logs -t --since="2018-02-08T13:23:37" CONTAINER_ID
```

查看某时间段日志：

```shell
$ docker logs -t --since="2018-02-08T13:23:37" --until "2018-02-09T12:23:37" CONTAINER_ID
```

Ylz@2i

docker run -it -d --name icbs --network=host -v ${ICBS_PREFIX}:/icbs hub.baasze.com/lab/icbs-centos:v2.0.7
