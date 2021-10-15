# 使用 docker 测试 webase 的一键部署

### docker

```
# 使用centos7镜像
docker pull centos:centos7

# --privileged模式运行镜像
# 在CentOS7下如果默认不加init启动命令的话不会启动systemd进程的，报错docker Failed to get D-Bus connection
docker run -itd --name webae-base  --network=host  --privileged=true \
 -p 30300:30300 \
 -p 30301:30301 \
 -p 20200:20200 \
 -p 20201:20201 \
 -p 8545:8545 \
 -p 8546:8546 \
 \
 -p 5100:5100 \
 -p 5101:5101 \
 -p 6021:6021 \
 -p 6020:6020 \
 \
 -p 6001:6001 \
 -p 6000:6000 \
 \
 -p 6010:6010 \
 -p 6011:6011 \
 \
 -p 6100:6100 \
 -p 6101:6101 \
 \
 -p 5000:5000 \
 -p 5001:5001 \
 -p 5002:5002 \
 -p 5003:5003 \
 -p 5004:5004 \
 \
 -p 3306:3306 \
 -p 6379:6379 \
 -P centos7-webase:0.0.1  /sbin/init
# 进入容器内部
 docker exec -it centos7-webase /bin/bash

docker run -itd --name webase  --network=host -v /root/webase-deploy:/root/webase-deploy --privileged=true  centos:centos7  /sbin/init

# 安装依赖

yum install openssl curl wget git dos2unix unzip zip vim sudo


## centos 更新系统时区

timedatectl set-timezone Asia/Shanghai


## 安装nginx
yum install epel-release

yum install nginx

## java


mkdir /software  && cd /software

https://download.oracle.com/otn/java/jdk/8u291-b10/d7fc238d0cbf4b0dac67be84580cfb4b/jdk-8u291-linux-x64.tar.gz?AuthParam=1630664044_907e08886debc9505dbeca2628875e2e

docker cp ~/Downloads/jdk-8u291-linux-x64.tar.gz  centos7:/software
# 解压jdk
$  tar -zxvf jdk-8u291-linux-x64.*

# 配置Java环境，编辑/etc/profile文件
$ vim /etc/profile

# 打开以后将下面三句输入到文件里面并保存退出

tar -zxvf jdk
export JAVA_HOME=/software/jdk1.8.0_291
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar


vim /etc/profile
# 设置 utf-8格式
export LANG=en_US.UTF-8
export LC_CTYPE=en_US.UTF-8

source /etc/profile

# 解决每次重进docker找不到环境变量的问题
echo "source /etc/profile"  >> ~/.bashrc
```

docker 启动报错

```
INFO[0000] libcontainerd: new containerd process, pid: 624
WARN[0001] devmapper: Usage of loopback devices is strongly discouraged for production use. Please use `--storage-opt                    dm.thinpooldev` or use `man docker` to refer to dm.thinpooldev section.
WARN[0001] devmapper: XFS is not supported in your system. Either the kernel doesn't support it or mkfs.xfs is not in                    your PATH. Defaulting to ext4 filesystem
INFO[0001] devmapper: Creating filesystem ext4 on device docker-0:46-2619452-base
INFO[0001] devmapper: Error while creating filesystem ext4 on device docker-0:46-2619452-base: exec: "mkfs.ext4": exe                   cutable file not found in $PATH
WARN[0001] devmapper: Failed to deactivatePool: Device is Busy
FATA[0001] Error starting daemon: error initializing graphdriver: exec: "mkfs.ext4": executable file not found in $PATH
```

这里有两个问题，一个是 没有安装 iptables，另一个问题是 没有 mkfs.ext4 命令，执行 yum install -y e4fsprogs iptables.x86_64 然后加载 ext4 模块， modprobe ext4 再执行 dockerd 发现能启动了
虽然有 error 但不影响使用，然后 Ctrl^c 退出，使用 systemctl enable docker 允许自启动，systemctl start docker 启动 Docker，

```
 dockerd --debug #docker
 yum install -y e4fsprogs iptables.x86_64
 modprobe ext4
systemctl start docker

```

## 数据库

数据库版本建议在 5.7 版本以下，5.7 版本以上需要禁用 only_full_group_by
https://github.com/WeBankFinTech/WeBASE-Node-Manager/issues/366

yum install -y mariadb\*

systemctl start mariadb.service

systemctl enable mariadb.service

初始化

```
 mysql_secure_installation

# 以下根据提示输入：
# Enter current password for root (enter for none):<–初次运行直接回车
# Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
# New password: <– 设置root用户的密码
# Re-enter new password: <– 再输入一次你设置的密码
# Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车
# Disallow root login remotely? [Y/n] <–是否禁止root远程登录，回车
# Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车
# Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车
#

mysql -u root -p

# 授权root用户远程访问
mysql > GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'ylzioDFCVvG2x' WITH GRANT OPTION;
mysql > flush PRIVILEGES;

mysql > create database webasenodemanager;

mysql > create database webasesign;




```

## Python

```
yum install -y python36
ym install -y python36-pip
pip3 install PyMySQL

```

## 拉取

```
wget https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBASE/releases/download/v1.5.1/webase-deploy.zip

unzip webase-deploy.zip

cd webase-deploy

# 修改配置

vim common.properties


mysql.user=root
mysql.password=123456
mysql.database=webasenodemanager

sign.mysql.user=root
sign.mysql.password=123456
sign.mysql.database=webasesign

encrypt.type=1
encrypt.sslType=1

```

## python 命令

```
# 一键部署
部署并启动所有服务        python3 deploy.py installAll
停止一键部署的所有服务    python3 deploy.py stopAll
启动一键部署的所有服务    python3 deploy.py startAll
# 各子服务启停
启动FISCO-BCOS节点:      python3 deploy.py startNode
停止FISCO-BCOS节点:      python3 deploy.py stopNode
启动WeBASE-Web:          python3 deploy.py startWeb
停止WeBASE-Web:          python3 deploy.py stopWeb
启动WeBASE-Node-Manager: python3 deploy.py startManager
停止WeBASE-Node-Manager: python3 deploy.py stopManager
启动WeBASE-Sign:        python3 deploy.py startSign
停止WeBASE-Sign:        python3 deploy.py stopSign
启动WeBASE-Front:        python3 deploy.py startFront
停止WeBASE-Front:        python3 deploy.py stopFront
# 可视化部署
部署并启动可视化部署的所有服务  python3 deploy.py installWeBASE
停止可视化部署的所有服务  python3 deploy.py stopWeBASE
启动可视化部署的所有服务  python3 deploy.py startWeBASE
```

# 默认端口占用

FISCO BCOS 网络端口讲解[https://mp.weixin.qq.com/s/XZ0pXEELaj8kXHo32UFprg]
根据节点数量递增
20200 20201 20202 20203 ++ Channel
8545 8546 8547 8548 ++ jsonrpc

30300 30301 30302 30303 ++ p2p

#webase
5004 webase-sign
5003
5002 webase-front
5001 webase-node-mrg
5000 webase-web

#weid-build-tool
5100
5101 /weid/weid-build-tools/webase-browser/api/\*
6021 可视化配置页面端口
6020 可视化配置后端端口？？

#WeIdentity RestService
6001 https/https
6000 当开启 HTTPS 时的 HTTP 请求端口，默认未实现重定向，可忽略

#WeIdentity Endpoint Service
#RPC 服务端的监听端口
6010
6011

#weid-sample
6100 https port server.port
6101 http port

6379 redis 视情况
3306 mysql 数据库 视情况
27017 mongo

tar -zxvf jdk
export JAVA_HOME=/root/jdk1.8.0_291
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

source /etc/profile

cd /root/webase-front/conf

mkdir gm

cp 证书 ./gm
