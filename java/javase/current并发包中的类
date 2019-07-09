### 1.Unsafe

  1. cas的检查方法
    /**
* 比较obj的offset处内存位置中的值和期望的值，如果相同则更新。此更新是不可中断的。
*
* @param obj 需要更新的对象
* @param offset obj中整型field的偏移量
* @param expect 希望field中存在的值
* @param update 如果期望值expect与field的当前值相同，设置filed的值为这个新值
* @return 如果field的值被更改返回true
*/
public native boolean compareAndSwapObject(Object obj, long offset, int expect, int update);

  2.获取内存的方法
    对应的内存偏移地址" ： unsafe.objectFieldOffset(field)
    ```java
    // 实例化Player
      Player player = (Player) unsafe.allocateInstance(Player.class);
      player.setAge(18);
      player.setName("li lei");
      for (Field field : Player.class.getDeclaredFields()) {
         System.out.println(field.getName() + ":对应的内存偏移地址" + unsafe.objectFieldOffset(field));
      }
    ```

###  AtomicStampedReference
从Java1.5开始JDK的atomic包里提供了一个类AtomicStampedReference来解决ABA问题。这个类的compareAndSet方法作用是首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志，如果全部相等，则以原子方式将该引用和该标志的值设置为给定的更新值。
---------------------
作者：逆月林
来源：CSDN
原文：https://blog.csdn.net/niyuelin1990/article/details/78557286
版权声明：本文为博主原创文章，转载请附上博文链接！
