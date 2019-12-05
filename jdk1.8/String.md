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
    // 两个对象equals为true，这两个对象的hash值相等；两个对象equals为false，这两个对象的hash值不一定不同
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
    // 线程执行wait()方法，会释放当前的锁，让出CPU，进入等待状态
    public final void wait() throws InterruptedException {
        wait(0);
    }
    protected void finalize() throws Throwable { }
}
    
```

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

###### equals方法

```java
// 比较两个字符串的内容
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

###### hashCode方法

```java
public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            // 31 * i = (i << 5) - i
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

##### AbstractStringBuilder

###### 类的属性

```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    // 存储字符序列中的字符，动态数组，存储容量不足时会扩容
    char[] value;
    // value数组中已存储的字符数
    int count;
}
```

###### append方法

```java
public AbstractStringBuilder append(String str) {
    if (str == null)
        return appendNull();
    int len = str.length();
    ensureCapacityInternal(count + len);
    // 将原来的数据复制到扩容后的数组中
    str.getChars(0, len, value, count);
    count += len;
    return this;
}
```

###### 扩容方法

```java
private void ensureCapacityInternal(int minimumCapacity) {
    // overflow-conscious code
    if (minimumCapacity - value.length > 0) {
        value = Arrays.copyOf(value,
                              newCapacity(minimumCapacity));
    }
}

private int newCapacity(int minCapacity) {
    // 新容量为原容量的2倍加2
    int newCapacity = (value.length << 1) + 2; // overflow-conscious code
    if (newCapacity - minCapacity < 0) {
        newCapacity = minCapacity;
    }
    return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
        ? hugeCapacity(minCapacity)
        : newCapacity;
}

private int hugeCapacity(int minCapacity) {
    if (Integer.MAX_VALUE - minCapacity < 0) { // overflow
        throw new OutOfMemoryError();
    }
    return (minCapacity > MAX_ARRAY_SIZE)
        ? minCapacity : MAX_ARRAY_SIZE;
}
```

##### StringBuilder

```java
// 线程不安全
public final class StringBuilder
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{
}
```

##### StringBuffer

```java
// 线程安全 
public final class StringBuffer
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{
    // 用来缓存上一次toString方法的返回值，一旦修改StringBuffer，置为null
    private transient char[] toStringCache;
}
```

