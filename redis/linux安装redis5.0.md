1. wget  redis 并解压 放在 /usr/local/ 较好
2.解压后 cd 进redis的src 目录 ，再执行 make 指令进行编译，前提示有安装gcc c编译器。
3. 最好在redis文件夹新建目录 bin ,把src下的启动文件（
  mkreleasehdr.sh
  redis-benchmark
  redis-check-aof
  redis-cli
  redis-server

）
   放在 redis 的bin，再建一个etc放redis 的src下的配置文件redis.conf .
4. 配置redis.conf文件。  vim redis.conf  配置如下：
   把 redis.conf配置文件中的 bind 127.0.0.1 这一行给注释掉，这里的bind指的是只有指定的网段才能远程访问这个redis，注释掉后，就没有这个限制了。
   把 redis.conf配置文件中的 protected-mode 设置成no（默认是设置成yes的， 防止了远程访问，在redis3.2.3版本后）
  （1）在配置文件中找到这一行 # requirepass foobared
  （2）删除前面的注释符号#，并把foobared修改成自己的密码  或者  另起一行 requirepass 自己的密码
  把文件中的daemonize属性改为yes（表明需要在后台运行）
5.设置Redis开机启动（）

[root@localhost etc]# vi /etc/rc.d/rc.local
   添加如下代码到 /etc/rc.d/rc.local 中：

/root/redis5/bin/redis-server  /root/redis5/etc/redis.conf
---------------------
作者：零碎de記憶
来源：CSDN
原文：https://blog.csdn.net/qq_39135287/article/details/83474865
版权声明：本文为博主原创文章，转载请附上博文链接！
