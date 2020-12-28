### 一.基本安装
  1. 需要三个基本节点 ：
    (1) master:主节点，类似 服务中心。
    (2) data节点： 主要存储索引数据。（分片备份存储机制），
        分片：把数据切成多块，放在多个机器存储，可拓展。
        备份存储：高可用，提高搜索性能。
    (3) client ：给搜索做负载均衡,机器太少不用。
  默认机制:主节点候选人，默认都是数据节点 。

  2.界面监控： head插件，kibanna .


  ### 二.master节点配置。
    1.安装es7.2   wget

    2.修改es7.2的 elasticsearch.yml的network为
       ` network.host 0.0.0.0 `
    3.vim /etc/sysctl.conf   最后面添加： vm.max_map_count=262144  ：最大文件数 ，
     保存退出后， systcl -p :生效
    4. 修改允许打开的最大文件描述符数量 vim /etc/security/limits.conf

    添加用户权限：
      esuser soft nofile 65536
      esuser soft  nproc 4096
      esuser hard   nofile 65536
      esuser hard   nproc 4096
    5.设置最大线程数 ,    vim /etc/security/limits.d/20-nproc.conf
      将 “ * ” 改为具体 esuser 用户

    6.建用户组 和添加 esuer 用户
      groupadd esgroup
      useradd esuser -g esgroup -p 123456

    7.创建文件归属用户 ：chown esuser:esgroup .

    8。切换用户，并启动  ，启动前可通过 config/jvm.options 设置内存大小：
      Xms256m
      Xmx256m
     启动： ./elesticsearch  -d :后端启动
