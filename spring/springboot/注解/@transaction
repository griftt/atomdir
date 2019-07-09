1.####使用了该注解的方法，都具有事务的特性。即对应的ACID特性。
2.####用法
  隔离性 isolation:  对应四个隔离性，这个参数有相应的枚举Isolation
  传播行为 popergation：
  REQUIRED（默认）：支持使用当前事务，如果当前事务不存在，创建一个新事务。
    SUPPORTS：支持使用当前事务，如果当前事务不存在，则不使用事务。
    MANDATORY：中文翻译为强制，支持使用当前事务，如果当前事务不存在，则抛出Exception。
    REQUIRES_NEW：创建一个新事务，如果当前事务存在，把当前事务挂起。
    NOT_SUPPORTED：无事务执行，如果当前事务存在，把当前事务挂起。
    NEVER：无事务执行，如果当前有事务则抛出Exception。
    NESTED：嵌套事务，如果当前事务存在，那么在嵌套的事务中执行。如果当前事务不存在，则表现跟REQUIRED一样。
    重点讲一下requires
    ---------------------
    作者：青鱼入云
    来源：CSDN
    原文：https://blog.csdn.net/u011305680/article/details/79206408
    版权声明：本文为博主原创文章，转载请附上博文链接！

  rollbackFor 回滚异常类型：
  demo:
3.####常见问题
@Transactional的事务开启 ，或者是基于接口的 或者是基于类的代理被创建。
所以在同一个类中一个无事务的方法调用另一个有事务的方法，事务是不会起作用的

 （1）同类中事务不生效问题  ：
     解决方法：
       1。使用如下代码可调用自己的另一个同类的事务方法。
       ```java
       CityServiceImpl proxy=(CityServiceImpl)AopContext.currentProxy();
       ```
      2. 使用 AspectJ 取代 Spring AOP 代理方式也可以解决上面非public和类内部方法调用事务不生效的问题
  (2)原因： spring代理对象的问题。
