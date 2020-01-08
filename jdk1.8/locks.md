[TOC]



##### Lock

```java
public interface Lock {
	// 获取锁
    void lock();
    // 调用此方法去获取锁时，如果线程正在等待获取锁，则这个线程能够响应中断，即中断线程的等待状态
    void lockInterruptibly() throws InterruptedException;
    // 尝试获取锁，如果获取成功则返回true。方法会立即返回
	boolean tryLock();
    // 在时间期限内未获取锁则返回false。未获取锁会等待一定的时间
	boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    // 释放锁
    void unlock();
    // 返回绑定到此Lock的新的Condition实例
    Condition newCondition();
}
```

##### Condition

```java
public interface Condition {
    // 当前线程进入等待状态直到被通知（signal）或中断
    void await() throws InterruptedException;
    // 当前线程进入等待状态直到被通知，该方法不响应中断
    void awaitUninterruptibly();
    // 当前线程进入等待状态直到被通知、中断或者超时，返回值表示剩余超时时间
    long awaitNanos(long nanosTimeout) throws InterruptedException;
    boolean await(long time, TimeUnit unit) throws InterruptedException;
    // 当前线程进入等待状态直到被通知、中断或者到某个时间。如果没有到指定时间就被通知，返回true
    boolean awaitUntil(Date deadline) throws InterruptedException;
    // 唤醒一个等待在Condition上的线程
    void signal(); 
    // 唤醒所有等待在COndition上的线程
    void signalAll();
}
```

##### ReentrantLock

```java
// 可重入锁，即一个持有锁的线程可以对资源重复加锁而不会阻塞
public class ReentrantLock implements Lock, java.io.Serializable {
}
```

##### ReentrantReadWriteLock

```java
public class ReentrantReadWriteLock
        implements ReadWriteLock, java.io.Serializable {
}
```

