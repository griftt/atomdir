1. 问题：
   启动Tomcat错误 JRE_HOME错误 :The JRE_HOME environment variable is not defined correctly
2.原因解决方法：
  创建一个maven项目后想启动tomcat测试一下新建的maven项目，结果一直报The JRE_HOME environment variable is not defined correctly，然后就会导致报出Application Server was not connected before run configuration stop, reason: Unable to ping server at localhost:1099等错误，分析了一大通，根据网上查到的一些说法，启动Tomcat后startup.bat脚本调用了catalina.bat，然后catalina.bat调用了setclasspath.bat，setclasspath.bat的头部定义了JAVA_HOME和JER_HOME,所以基本确定的错误的地方就在这里：
3.解决方法：
确定jdk是否正确安装，java -verbose 查看jdk安装的位置，再在my配置如下在
set JAVA_HOME=E:\Java\jdk1.7.0_45（以我安装的jdk位置为例）
set JRE_HOME=E:\Java\jdk1.7.0_45\jre
