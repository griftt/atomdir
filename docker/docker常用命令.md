docker pull
docker push 镜像名  (仓库名/镜像的名字) 否则会报requested access to the resource is denied
1.docker commit :提交镜像
2. docker ps -a :查看所有container
3.docker build -t 镜像名 . :基于当前目录build

docker exec  -it  iamgeID  /bin/bash:进入容器

docker rmi imagename：tag 或imageId 删除镜像
docker rmi $(docker images -q)  //删除所有镜像

docker rm $(docker ps -aq)  //删除所有容器

 docker stop $(docker ps -aq) 停止所有容器

## run

docker run -d -P 80:90 image  后台启动某个容器
docker run  link  a:b   链接一个容器，其中a:容器名 b: 通道别名
