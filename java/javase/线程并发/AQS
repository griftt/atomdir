### AbstractQueuedSynchronizer
>https://blog.csdn.net/u010325193/article/details/86590169
AQS 全称 AbstractQueuedSynchronizer，实现他的类有Semaphore、CountDownLatch、ReentrantLock、ReentrantReadWriteLock、FutureTask。

AQS 中有两个重要的成员：
成员变量 state。用于表示锁现在的状态，用 volatile 修饰，保证内存一致性。
同时所有对 state 的操作都是使用 CAS 进行的。state 为0表示没有任何线程持有这个锁，线程持有该锁后将 state 加1，释放时减1。多次持有释放则多次加减。
还有一个双向链表，链表除了头结点外，每一个节点都记录了线程的信息，代表一个等待线程。这是一个 FIFO 的链表。
  ##### Node节点内部
      队列是默认有一个 head 节点的，并且不包含线程信息。上面情况3中，addWaiter 会创建一个 Node，并添加到链表的末尾，Node 中持有当前线程的引用。
      同时还有一个成员变量 waitStatus，表示线程的等待状态，初始值为0。我们还需要关注两个值：

        * CANCELLED，值为1，表示取消状态，就是说我不要这个锁了，请你把我移出去。
        * SINGAL，值为-1，表示下一个节点正在挂起等待，注意是下一个节点，不是当前节点。
          同时，加到链表末尾的操作使用了 CAS+死循环的模式，很有代表性，：

下面以 ReentrantLock 非公平锁的代码看看 AQS 的原理。
  #### ReentrantLock
  ReentrantLock 使用代码实现了和 synchronized 一样的语义，包括可重入，保证内存可见性和解决竞态条件问题等。相比 synchronized，它还有如下好处：
    * 支持以非阻塞方式获取锁
    * 可以响应中断
    * 可以限时
    * 支持了公平锁和非公平锁
       ReentrantLock 内部有两个内部类，分别是 FairSync 和 NoFairSync，对应公平锁和非公平锁。他们都继承自 Sync。Sync 又继承自AQS。
```java
    // 该锁的基本使用
    public class Counter {
        private final Lock lock = new ReentrantLock();
        private volatile int count;

        public void incr() {
            lock.lock();
            try {
                count++;
            } finally {
                lock.unlock();
            }
        }

        public int getCount() {
            return count;
        }
    }
```

#### 请求锁

请求锁时有三种可能：

  * 如果没有线程持有锁，则请求成功，当前线程直接获取到锁。
  * 如果当前线程已经持有锁，则使用 CAS 将 state 值加1，表示自己再次申请了锁，释放锁时减1。这就是可重入性的实现。
  * 如果由其他线程持有锁，那么将自己添加进等待队列。

####线程挂起
  *  如果此节点的上一个节点是头部节点，则再次尝试获取锁，获取到了就移除并返回。获取不到就进入下一步；
  *  判断前一个节点的 waitStatus，如果是 SINGAL，则返回 true，并调用 LockSupport.park() 将线程挂起；
  *  如果是 CANCELLED，则将前一个节点移除；
  *  如果是其他值，则将前一个节点的 waitStatus 标记为 SINGAL，进入下一次循环。

可以看到，一个线程最多有两次机会，还竞争不到就去挂起等待。
```java
final boolean acquireQueued(final Node node, int arg) {
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                return interrupted;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } catch (Throwable t) {
        cancelAcquire(node);
        throw t;
    }
}
```
#### 释放锁
  释放锁
    * 调用 tryRelease，此方法由子类实现。实现非常简单，如果当前线程是持有锁的线程，就将 state 减1。减完后如果 state 大于0，表示当前线程仍然持有锁，返回 false。如果等于0，
    表示已经没有线程持有锁，返回 true，进入下一步；
    * 如果头部节点的 waitStatus 不等于0，则调用LockSupport.unpark()唤醒其下一个节点。头部节点的下一个节点就是等待队列中的第一个线程，这反映了 AQS 先进先出的特点。
      另外，即使是非公平锁，进入队列之后，还是得按顺序来
#### 公平锁和非公平锁
    公平锁是在争抢资源是先判断state是否为空，在竞争锁之前判断一下等待队列中有没有线程在等待就行了，有的话就把自己加进等待队列。
    非公平锁直接争抢资源，抢到的话就state加一。
