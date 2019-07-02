###maven多模块打包时报找不到主类

1.异常信息：unalble to find main class

2.处理方法：
  为父工程的pom文件中plugin添加<pluginManagement>标签
  ```` java
  <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </pluginManagement>
  ```

  因为继承了父工程，所以打包时也会使用插件按可执行jar打包
