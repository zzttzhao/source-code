[TOC]



##### Object

```java
public class Object {
    private static native void registerNatives();
    static {
        registerNatives();
    }
    // 返回此Object运行时的类
    public final native Class<?> getClass();
    // 返回对象的hash值
    public native int hashCode();
    // 比较对象的内存地址
    public boolean equals(Object obj) {
        return (this == obj);
    }
    protected native Object clone() throws CloneNotSupportedException;
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
    // 唤醒在此对象监视器上等待的单个线程
    public final native void notify();
    // 唤醒在此对象监视器上等待的所有线程
    public final native void notifyAll();
    public final native void wait(long timeout) throws InterruptedException;
    public final void wait(long timeout, int nanos) throws InterruptedException {
        if (timeout < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }
        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }
        if (nanos > 0) {
            timeout++;
        }
        wait(timeout);
    }
    public final void wait() throws InterruptedException {
        wait(0);
    }
    protected void finalize() throws Throwable { }
}
    
```

1 equals()、hashCode()

两个对象equals为true，这两个对象的hash值相等；两个对象equals为false，这两个对象的hash值不一定不同

2 wait()、notify/notifyAll()

线程执行wait()方法，会释放当前的锁，让出CPU，进入等待状态



##### String

###### 类的属性

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    // 用来存储字符串
    private final char value[];
    // 缓存字符串的hash值
    private int hash; // Default to 0
    private static final long serialVersionUID = -6849794470754667710L;
}
```

1 equals()

比较两个字符串的内容