
 IPv4 forwarding is disabled. Networking will not work

centos7系统下 docker 环境搭建
运行环境: VMware Workstation Pro 在虚拟机中安装centos7系统, 选择最小安装, 网络连接方式选择的桥接(与宿主机在同一IP段)
centos7一定要安装64位, docker目前不支持32位内核

##看准了, 是在centos7系统下, centos6填了很多坑, 最终还是选择了用centos7系统

## 准备工作
因为是最小安装, 默认没有wget, 安装wget

yum install wget

打开阿里云的开源镜像站
http://mirrors.aliyun.com/help/centos

备份更新源 文件


mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 替换阿里云镜像源
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# 或者使用网易镜像源(推荐用这个, 最近阿里镜像源抽了, 经常无法访问)
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
复制代码
运行yum makecache生成缓存
运行yum update更新系统, 这个过程会比较慢

## 开始安装docker

yum install docker-io
安装完成最重要的一步是启动docker服务, 好多教程上都没有这个步骤, 当初在这点费了好大劲

service docker start
如果当前账户不是root的话,所有的docker命令前都要加上sudo

查看本地docker镜像列表

docker images #如果报错的话, 查看docker服务是否成功启动
## 登录阿里云镜像仓库

- 首先得有个阿里云账号, 并且已经通过实名认证
- 进入这个地址https://cr.console.aliyun.com/或这个地址https://dev.aliyun.com/search.html
- 登录之后, 进入管理控制台找到docker镜像仓库->镜像列表->然后给docker设置一个登录密码
- 创建一个镜像仓库, namespace随便起个, 镜像名称命名为centos-jdk7-tomcat7
- 仓库类型私有, 代码源为本地仓库, 然后创建
- centos-jdk7-tomcat7镜像仓库创建完成之后, 进入管理界面, 有详细的操作指南


### 配置镜像加速器

- 设置镜像加速器, 在管理控制台 Docker镜像仓库->加速器 每个用户都会创建一个专属加速器
- 修改daemon配置文件/etc/docker/daemon.json来使用加速器

复制代码
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://cvv5tle1.mirror.aliyuncs.com"]# 这里的镜像源是我专属的, 其他人用可能起不到加速效果
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
复制代码
### 在本地登录阿里云镜像仓库

docker login --username=yzzhouyalei@foxmail.com registry.cn-hangzhou.aliyuncs.com
用户名是阿里云账号全称, 密码是开通namespace时设置的密码(可以在阿里云控制台修改密码)
执行pull命令下载一个centos, 版本为latest的镜像

docker  pull centos # 不指定版本的话, 默认下载latest版本, 或者执行docker search centos, 搜索一个centos镜像
下载完成后, 执行docker images 查看本地镜像

[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/centos    latest              67591570dd29        8 weeks ago         191.8 MB
启动一个基于docker.io/centos的容器

docker run -it --name=test-centos docker.io/centos:latest /bin/bash
-it 表示交互模式, 启动成功后进入命令行
-d 表示守护模式, 容器在后台运行
--name 为容器命名
docker.io/centos:latest 格式为镜像名:版本
/bin/bash 进入bash命令行
命令执行结果

[root@localhost ~]# docker run -it --name=test-centos docker.io/centos:latest /bin/bash
[root@060e31db603c /]# 已经进入容器内部
因为目前的容器内部, 没有任何程序运行, 所以一旦执行exit, 退出容器时, 容器将结束运行


[root@060e31db603c /]# exit
exit
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS                      PORTS               NAMES
060e31db603c        docker.io/centos:latest   "/bin/bash"         3 minutes ago       Exited (0) 11 seconds ago                       test-centos
[root@localhost ~]#
复制代码
执行exit, 此时, 运行docker ps -a 查看容器, 发现状态为Exited, 运行中的状态是Up

docker ps 命令, 查看运行中的容器
docker ps -a命令, 查看全部的容器, 包括运行中和已停止运行的容器
删除状态为Exited, 已停止运行的容器

通过容器名称删除已经停止运行的容器(test-centos为docker run命令中给容器的命名)
docker rm test-centos
通过容器ID删除已经停止运行的容器
docker rm 060e 这两个命令效果一样, 060e是containerID的前4位
强制删除状态为Up, 正在运行中的容器

docker rm -f test-centos




到此步骤, centos7系统下docker环境搭建已经完成
