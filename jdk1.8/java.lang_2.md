[TOC]



##### Thread

###### 类的属性

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
    private boolean  stillborn = false;
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
    // 此线程的本地变量值，是一个用于维护线程本地变量的hashmap
    ThreadLocal.ThreadLocalMap threadLocals = null;
    // 
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    //
    private long stackSize;
    // 
    private long nativeParkEventPointer;
    //
    private long tid;
    //
    private static long threadSeqNumber;
    //
    private volatile int threadStatus = 0;
    //
    volatile Object parkBlocker;
    //
    private volatile Interruptible blocker;
    //
    private final Object blockerLock = new Object();
    //
    public final static int MIN_PRIORITY = 1;
    //
    public final static int NORM_PRIORITY = 5;
    //
    public final static int MAX_PRIORITY = 10;
    //
    private static final StackTraceElement[] EMPTY_STACK_TRACE
        = new StackTraceElement[0];
    //
    private static final RuntimePermission SUBCLASS_IMPLEMENTATION_PERMISSION =
                    new RuntimePermission("enableContextClassLoaderOverride");
    //
    private volatile UncaughtExceptionHandler uncaughtExceptionHandler;
    private static volatile UncaughtExceptionHandler defaultUncaughtExceptionHandler;
    
    
    long threadLocalRandomSeed;
    int threadLocalRandomProbe;
    int threadLocalRandomSecondarySeed;
}
```

###### 线程状态枚举值

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

