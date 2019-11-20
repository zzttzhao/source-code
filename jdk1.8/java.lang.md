[TOC]



###### Object

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

