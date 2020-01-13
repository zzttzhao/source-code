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
    private static final long serialVersionUID = 7373984872572414699L;
}
```

###### Sync同步器

```java
abstract static class Sync extends AbstractQueuedSynchronizer {
    private static final long serialVersionUID = -5179523762034025860L;
    abstract void lock();
    // 非公平模式下的尝试获取锁
    final boolean nonfairTryAcquire(int acquires) {
        // 获取当前线程
        final Thread current = Thread.currentThread();
        // 获取AQS中的state（独占锁是否被获取）
        int c = getState();
        // 独占锁没有被获取
        if (c == 0) {
            if (compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        // 判断当前线程是否为获取独占锁的线程
        else if (current == getExclusiveOwnerThread()) {
            // 重入获取锁
            int nextc = c + acquires;
            if (nextc < 0) // overflow
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
    // 尝试释放锁
    protected final boolean tryRelease(int releases) {
        int c = getState() - releases;
        if (Thread.currentThread() != getExclusiveOwnerThread())
            throw new IllegalMonitorStateException();
        boolean free = false;
        if (c == 0) {
            free = true;
            setExclusiveOwnerThread(null);
        }
        setState(c);
        return free;
    }
}
```

###### NonfairSync非公平模式

```java
static final class NonfairSync extends Sync {
    private static final long serialVersionUID = 7316153563782823691L;
    final void lock() {
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1);
    }
    // 尝试获取锁，非公平模式
    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
}
```

###### FairSync公平模式

```java
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;
    final void lock() {
        acquire(1);
    }
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            // hasQueuedPredecessors()方法判断是否有等待获取锁资源线程并且不是当前线程，有则不去竞争
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
}
```

##### ReentrantReadWriteLock

```java
public class ReentrantReadWriteLock
        implements ReadWriteLock, java.io.Serializable {
}
```

