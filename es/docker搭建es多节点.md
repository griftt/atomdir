参考链接  https://www.cnblogs.com/wunaozai/p/9928962.html
https://blog.csdn.net/u011781521/article/details/80464826
https://www.cnblogs.com/mxmbk/p/9969008.html
### 1. 首先启动单节点测试
docker run -d --name es-main -p 9200:9200
-e http.port=9200 -e network.host=0.0.0.0
-e http.cors.allow-origin="*" -e http.cors.enabled=true
-e discovery.type="single-node"
-e  http.cors.allow-headers="*"
-e "ES_JAVA_OPTS=-Xms256m -Xmx256m"
-e http.cors.allow-credentials=true
elasticsearch:6.4.2
出现容器 ： 9d78b5ba496d6b2604f424fb4b5e7819243baaa75478fd38cf4f47d021685a49
接着 curl  127.0.0.1:9200 测试。
### 2.搭建其他数据节点
     #### 创建一个虚拟网络
      - docker network create --driver bridge --subnet 172.22.17.0/24 --gateway 172.22.17.254 es_net
      ####  启动一个节点
          # node1
      docker run -d --name es_1 --net es_net --ip 172.22.17.1 \
      -p 2101:9200 -p 3101:9300 -v /etc/localtime:/etc/localtime \
      -e "cluster.name=node1" \
      -e "node.name=node1" \
      -e "network.host=172.22.17.1" \
      -e "network.bind_host=0.0.0.0" \
      -e "ES_JAVA_OPTS=-Xms256m -Xmx256m" \
      -e "discovery.zen.ping.unicast.hosts=172.22.17.1,172.22.17.2,172.22.17.3" \
      -e "discovery.zen.minimum_master_nodes=1" \
      -e "http.port=9200" \
      -e "http.cors.allow-origin=*" \
      -e "http.cors.enabled=true" \
      -e "http.cors.allow-headers=X-Requested-With,X-Auth-Token,Content-Type,Content-Length,Authorization" \
      -e "http.cors.allow-credentials=true" \
      elasticsearch:6.4.2

### 运行elastichd （es的数据可视化面板）
docker run -p 9800:9800 -d --link es:demo containerize/elastichd

### 这里要注意的问题 ：
 (1)  -e "ES_JAVA_OPTS=-Xms256m -Xmx256m" ： 设置内存为256m ,默认为1g，如果太大可能会导致，启动不了，或者启动后就立即退出了。
 (2)  WARNING: IPv4 forwarding is disabled. Networking will not work.
     更改 /etc/sysctl.conf中的“ net.ipv4.ip_forward=1”，默认其实是0.
     更改后执行“systemctl restart network”，再次启动elasticsearch则可以通过外网访问！
  (3) 出现权限问题时，要在相应的挂载文件夹上，执行
      - chown 1000:1000  文件目录名称
