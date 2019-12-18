[TOC]



#### Thread

##### 类的属性

```java
public
class Thread implements Runnable {
    // 线程名
    private volatile String name;
    // 线程优先级
    private int priority;
    private Thread threadQ;
    private long eetop;
    // 是否单步执行此线程
    private boolean single_step;
    // 此线程是否为守护线程
    private boolean daemon = false;
    // JVM状态
    private boolean stillborn = false;
    // run方法执行的目标代码
    private Runnable target;
    // 此线程所属的组别
    private ThreadGroup group;
    // 此线程的类加载器
    private ClassLoader contextClassLoader;
    // 此线程继承的访问控制上下文
    private AccessControlContext inheritedAccessControlContext;
    // 用于自动编号的匿名线程
    private static int threadInitNumber;
    // 此线程的本地变量值，用于维护线程本地变量的hashmap，由ThreadLocal类进行维护
    ThreadLocal.ThreadLocalMap threadLocals = null;
    // 由继承得到的和此线程相关的本地变量值，由InheritableThreadLocal类进行维护
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    // 此线程请求栈的深度，默认值为0
    private long stackSize;
    // 表示在本地线程终止后,JVM的私有状态值
    private long nativeParkEventPointer;
    // 线程id
    private long tid;
    // 用于生成线程ID
    private static long threadSeqNumber;
    // 为工具提供的线程状态值，初始化值表示当前线程还未运行
    private volatile int threadStatus = 0;
    // 为用于调用java.util.concurrent.locks.LockSupport.park方法的参数
    volatile Object parkBlocker;
    // 在可中断I/O操作中，本线程中的此对象会被阻塞
    // 如果此线程的中断状态位被设置，则应该调用此阻塞对象的中断方法
    private volatile Interruptible blocker;
    private final Object blockerLock = new Object();
    // 线程的最低优先级
    public final static int MIN_PRIORITY = 1;
    // 线程的默认优先级
    public final static int NORM_PRIORITY = 5;
    // 线程的最高优先级
    public final static int MAX_PRIORITY = 10;
}
```

##### 线程状态枚举值

```java
public enum State {
    // 线程未开始，只是进行了线程创建的初始化操作，未调用start()方法
    NEW,
    // 线程正在运行或者处于就绪状态等待获取CPU
    RUNNABLE,
    // 线程调用wait()方法后，在等待锁，处于阻塞状态
    BLOCKED,
    // 线程调用Objct.wait()、Thread.join()或者LockSupport.park()方法后处于waiting状态
    // 一个线程等待另一个线程的特定操作（通知或中断），等待无时间限制
    WAITING,
    // 线程调用Thread.sleep()、Object.wait()[指定超时值]、Thread.join()[指定超时值]、LockSupport.parkNanos()或者LockSupport.parkUntil()方法后处于timed_waiting状态
    // 一个线程等待另一个线程的特定操作（通知或中断），等待有时间限制
    TIMED_WAITING,
    // 线程执行完成
    TERMINATED;
}
```

##### sleep方法

```java
// 当前线程停止执行指定毫秒数，让出CPU给其他的线程，不会释放锁
public static native void sleep(long millis) throws InterruptedException;
```

##### start方法

```java
// 当前线程执行，JVM会调用此线程的run()方法
// 多次start同一个线程，会抛出异常
public synchronized void start() {
    // 线程未开始，状态为NEW时，threadStatus值为0；否则会抛出异常
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    group.add(this);

    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
        }
    }
}
```

##### run方法

```java
// 线程有runnable对象则执行
public void run() {
    if (target != null) {
        target.run();
    }
}
```

##### interrupt方法

```java
// 中断当前线程，将该线程的中断标记设置为true
// 线程处于阻塞状态，调用interrupt()时，线程退出阻塞状态并抛出InterruptedException异常
public void interrupt() {
}
```

##### 判断线程是否中断

```java
// 判断当前线程是否被中断，线程的中断状态会被此方法清除
public static boolean interrupted() {
    return currentThread().isInterrupted(true);
}

// 判断当前线程是否被中断，此方法的调用不会影响当前线程的中断状态
public boolean isInterrupted() {
    return isInterrupted(false);
}

private native boolean isInterrupted(boolean ClearInterrupted);
```

##### join方法

```java
// 最多等待millis毫秒线程会死亡，参数为0时持续等待
// 在A线程中调用B线程.join()，表示直到B线程结束，A线程才继续运行
public final synchronized void join(long millis)
throws InterruptedException {
}
```

#### ThreadLocal

```java
public class ThreadLocal<T> {
}
```

##### ThreadLocalMap

###### 类的属性

```java
static class ThreadLocalMap {
    // 初始容量，大小总为2的幂次方
    private static final int INITIAL_CAPACITY = 16;
    // 存放元素的数组，大小总为2的幂次方
    private Entry[] table;
    // 存放的元素个数
    private int size = 0;
    // 临界值，当实际大小（存放元素的个数）超过临界值，进行扩容
    private int threshold; // Default to 0
}
```

###### Entry结点

```java
// ThreadLocal为key，key为null表示不再被引用，entry可以从table清除
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

###### set方法

```java
private void set(ThreadLocal<?> key, Object value) {
    Entry[] tab = table;
    int len = tab.length;
    // 计算索引
    int i = key.threadLocalHashCode & (len-1);
	// 循环，当前索引上的table[i]不为空，在没有结束循环的情况下，用线性探测法获取下一个，解决hash冲突
    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        ThreadLocal<?> k = e.get();
		// 更新value
        if (k == key) {
            e.value = value;
            return;
        }
		// key为空，说明被回收了，用新的key-value替换
        if (k == null) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }
	// 找到为空的位置插入值
    tab[i] = new Entry(key, value);
    int sz = ++size;
    // 没有清除任何entry且当前使用量超过长度的2/3，扩容
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}
```

###### get方法

```java
private Entry getEntry(ThreadLocal<?> key) {
    // 计算索引
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
    if (e != null && e.get() == key)
        return e;
    else
        return getEntryAfterMiss(key, i, e);
}

private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
    Entry[] tab = table;
    int len = tab.length;

    while (e != null) {
        ThreadLocal<?> k = e.get();
        if (k == key)
            return e;
        if (k == null)
            // 清除无效的entry
            expungeStaleEntry(i);
        else
            // 线性探测法获取下一个
            i = nextIndex(i, len);
        e = tab[i];
    }
    return null;
}
```

