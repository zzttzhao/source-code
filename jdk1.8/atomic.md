[TOC]



##### AtomicBoolean

###### 类的属性

```java
public class AtomicBoolean implements java.io.Serializable {
    private static final long serialVersionUID = 4654671469794556979L;
    // 设置为使用Unsafe.compareAndSwapInt进行更新
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    // value成员属性的内存地址相对于对象内存地址的偏移量
    private static final long valueOffset;
    // 初始化时执行静态代码块
    static {
        try {
            // 计算出value成员属性的内存地址相对于对象内存地址的偏移量
            valueOffset = unsafe.objectFieldOffset
                (AtomicBoolean.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }
    // boolean的值，默认为0
    private volatile int value;
}
```

###### get方法

```java
public final boolean get() {
    return value != 0;
}

// 通过原子的方式设置给定的值，并返回之前的值
public final boolean getAndSet(boolean newValue) {
    boolean prev;
    do {
        prev = get();
    } while (!compareAndSet(prev, newValue));
    return prev;
}

public final boolean compareAndSet(boolean expect, boolean update) {
    // 预期值
    int e = expect ? 1 : 0;
    // 更新值
    int u = update ? 1 : 0;
    //如果当前值等于预期值，则将该值原子设置为更新值
    return unsafe.compareAndSwapInt(this, valueOffset, e, u);
}
```

###### set方法

```java
public final void set(boolean newValue) {
    value = newValue ? 1 : 0;
}

// 最终设定为给定值
public final void lazySet(boolean newValue) {
    int v = newValue ? 1 : 0;
    unsafe.putOrderedInt(this, valueOffset, v);
}
```

##### AtomicInteger

###### 类的属性

```java
public class AtomicInteger extends Number implements java.io.Serializable {
    private static final long serialVersionUID = 6214790243416807050L;
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;
    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }
	// int的值
    private volatile int value;
}
```

##### AtomicLong

###### 类的属性

```java
public class AtomicLong extends Number implements java.io.Serializable {
    private static final long serialVersionUID = 1927816293512124184L;
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;
    private static native boolean VMSupportsCS8();
    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                (AtomicLong.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }
    private volatile long value;
}
```

##### AtomicReference

###### 类的属性

```java
public class AtomicReference<V> implements java.io.Serializable {
    private static final long serialVersionUID = -1848883965231344442L;
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;
    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                (AtomicReference.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }
    private volatile V value;
}
```

